# bat 🦇

<img src="https://github.com/vpavlenko/bat/assets/1491908/37e7cbd5-034d-436b-a00c-2e807d88647b" width="600">

A Beat As Token transformer architecture designed from scratch ~~to fail~~ to enhance beat token embeddings with pairwise beat comparisons.

## Idea

- Let's train BERT since we don't want to [generate](https://github.com/affige/genmusic_demo_list) anything.
- Let's take a MIDI file. If you don't have one, take your audio, run [demucs + basic-pitch](https://github.com/vpavlenko/study-music/blob/main/parts/transcription.md) and pack tracks into one
- Run beat tracking to know exact measure/beat onsets
- ~~Discard your file if it's not 4/4~~
- Write a function that gives you all notes participating in a single beat
- Now write a function that extracts binary features from a single beat. The more the better. Think relatively. Hey, music isn't about absolute pitches.
   - Is there a bass onset at this beat?
   - Eleven features for presence of all intervals above the bass?
   - Does the melody have one/two/four notes? Where do they go?
   - Is there a chord strumming?
   - Which quarter of the measure is it?
   - Does it have 16th hi-hats? Snare/kick/crash?
   - etc etc
- Make a bit vector out of these features. As we multiply it by a learnable matrix B, we'll get beat embeddings - these will be our tokens.
- By the way, we'll go for the context of 512 tokens - because most of the tracks fit under 128 measures.
- Now for the weird part. We need to make a first self-attention. A key-query-value dance. Let's calculate relative pairwise features between two beats and use it there.
- So, write a function that extracts binary features from a pair of beats. Again, no absolute pitches allowed:
   - Is the bass/chord/melody the same?
   - Is the melody from beat 1 tranposed to beat 2?
   - Are the bass/chord/melody starting from these two beats equal for the next 4/16/64 beats? (A bit of pre-compute for faster convergence.)
   - What's the interval between leftmost bass notes of two beats?
   - Is the second beat an exact transposition of all notes one tone up?
   - Encode relative distance in measures in beats between these two. Look up rotary position encoding and invent your own.
   - etc etc
- Then multiply this on Key/Query/Value matrices, make self-attention, add MLP.
- Stack more layers. Don't repeat the pairwise embeddings again - hope that a residual connection will help if need be. Use some standard self-attention with some relative distances.

## Where to start

Start learning this on tiny sizes with a handful of features and just 2 self-attention layers. Maybe try a tiny embedding length - 32? 

Oh, how do we train? What should [MASK] token be equal to? Well, let's do binary prediction of all beat features via a sigmoid. A loss will sum all sigmoids.


## Fine-tuning

We can try to fine-tune on answering music theory questions about every beat:
- is this a tonic chord
- is this a true measure start, a 4-measure phrasing start
- is this a start of a direct modulation up
- what's the likely local scale
- is this blues

## Literature

- https://arxiv.org/abs/2107.09877
- https://arxiv.org/abs/2203.14263
- https://arxiv.org/pdf/2111.07624.pdf
