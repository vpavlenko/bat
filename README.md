# bat 🦇

<img src="https://github.com/vpavlenko/bat/assets/1491908/37e7cbd5-034d-436b-a00c-2e807d88647b" width="600">

A transformer architecture designed from scratch ~~to fail~~ to enhance beat token embeddings with pairwise beat similarities.

## Idea

- Let's take a MIDI file. If you don't have one, take your audio, run [demucs + basic-pitch](https://github.com/vpavlenko/study-music/blob/main/parts/transcription.md) and pack tracks into one
- Run beat tracking to know exact measure/beat onsets
- ~~Discard your file if it's not 4/4~~
- Write a function that gives you all notes participating in a single beat
- Now write a function that extracts binary features from a single beat. The more the better. Think relatively. Hey, music isn't about absolute pitches.
   - Is there a bass onset at this beat?
   - Eleven features for presence of all intervals above the bass?
   - Does the melody have one/two/four notes? Where do they go?
   - Is there a chord strumming?
   - etc etc
- Make a bit vector out of these features. When we multiply it by a learnable matrix B, we'll get beat embeddings - these will be our tokens.
- By the way, we'll go for the size of 512 tokens - because most of the tracks fit under 128 measures.
  



