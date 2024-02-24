## Setup
- To get the best results, use the global instance setup
- Add 2 instances of the plugin, one for chords and the other for patterns
- Set the global track lookahead to 50 ms
- On each pattern track, in the track info panel, set the delay to 50 ms
- If you need to transpose notes, in the pattern instance, set `pattern octave wraparound` to `[Fixed] Every 13th pattern note`

This way, the DAW will delay the pattern notes for 50 ms, so that the chord instance can get the notes
first and then use the pattern after recording the current chord. The DAW will compensate for the delay