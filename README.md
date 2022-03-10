# Making Music with Code using TidalCycles course
Algorithmic Art Assembly – March 10 2022

### Learning the ropes 

```
-- making the first sounds: 
d1 $ s "bd bd"

-- most important command (silences everything):
hush 

-- silences the selected bus
d1 $ silence
```

### Making a simple beat

```
-- set tempo

setcps(1) 

setcps(120/120)

d1 $ s "bd bd"

d2 $ s "hh*4"

d3 $ s "~ cp"

hush
```

### Different ways to make patterns 

```
d1 $ "bd" -- set a downbeat 

d2 $ "~ cp" -- rest 

d2 $ "~ [cp cp]" -- subdivide

d2 $ "~ cp*2" -- similar

d2 $ "~ cp*<2 1>" -- alternate for each cycle

d2 $ "cp(3,8)" -- using euclidian rhythms

d2 $ "cp(6,8,1)" -- third parameter = offset 

d2 $ slow 2 $ "cp(6,8,1)" -- use slow function 
```

Bonus: Making the [amen break](https://www.youtube.com/watch?v=FFKMtv8tU0U)!

```
d1 $ slow 2 $ "[bd bd] ~ [~ bd] ~" 

d2 $ slow 2 $ "~ [cp ~ ~ cp] [~ cp ~ ~] cp"
```

### Melodies 
```
d1 $ note "c4" # "superpiano" -- make a simple note 

d1 $ note "c4 g4" # "superpiano" -- more notes

-- or use up to transpose the note in half steps (1 octave = 12 half steps)
d1 $ note "c4" # "superpiano" 
  + up "0 7"

-- stack multiple transpositions 

d1 $ note "c4" # "superpiano" 
  + up "0 7" 
  + up "<0 5>"

d1 $ note "c4" # "superpiano" 
  + up "0 4 7 12" 
  + up "<0 5 4 7>"

-- assign rhythms
d1 $ note "c4" # "superpiano" 
  + up "0*2 0" 
  + up "<0 5 4 7>"

d1 $ note "c4*2 c4" # "superpiano" 
  + up "<0 5 4 7>"
```

### Samples

View all samples: 

IN SUPERCOLLIDER
```
run: Quarks.gui
click: Dirt-Samples
click: Local path
```

Using 808 samples 
```
d1 $ "808" -- select samples in a folder 

d1 $ "808:1" -- select 1st index sample
d1 $ "808:2" -- select 2nd index sample

d1 $ fast 4 $ "808" # n (irand 10) -- select random samples

-- sample manipulation

d1 $ "808" # speed 0.5 -- half speed = one octave down

d1 $ "808" # speed "-1" -- reverse

d1 $ "808*4" # speed "0.5 2 1 0.25" -- different speeds 

d1 $ "808*4" # speed (rand) -- random speeds

d1 $ "808(5,8)" # speed (range 0.5 4 rand) -- random range
```

### Effects 
```
d1 $ "808(3,8)" # room 0.5 -- reverb

d1 $ "808(3,8)" # lpf 1500 # resonance 0.3 -- low-pass-filter

d1 $ "808(3,8)" # hpf 1500 # resonance 0.2 -- high-pass-filter

d1 $ "808(3,8)" # gain 1.2 -- volume (CAREFUL not to exeed ~1.5 unless using a limiter)

d1 $ "808(3,8)" # delay 0.5 # delayfb 0.5 # delayt (1/4) -- delayt

d1 $ stut 4 0.5 (1/4) $ "808(3,8)" -- using "stut" as delay

-- bringing it all together
d1 
  $ stut 4 0.5 (1/4) 
  $ "808(3,8)"
  # speed (range 0.5 4 rand) 
  # room 0.8
  # lpf (range 200 4000 rand)
  # resonance 0.1
```

### MIDI support

You will need to set this up in superCollider follow this: https://gist.github.com/dqgorelick/13b58e4f56a0c7c24dfd64357bfeb002 (this is for Mac)

SUPERCOLLIDER CODE:

```
MIDIClient.init;

(
~midiOut1 = MIDIOut.newByName("IAC Driver", "synth");

~dirt.soundLibrary.addMIDI(\synth, ~midiOut1);

~midiOut1.latency = 0;
)

MIDIClient.restart; // restarts client 
```

 
### Thinking in code 

These are examples of getting the same result but from different approaches. relates, different ways of "thinking in code"...

```
d1 $ s "clubkick*2"

d1 $ fast 2 $ s "clubkick"

d1 $ ply 2 $ s "clubkick"

d1 $ fastcat [s "clubkick", s "clubkick"]

d1 $ struct "11" $ s "clubkick"

d1 $ struct "1(2,4)" $ s "clubkick"

d1 $ struct "<1*2 [1 1] {1}%2>" $ s "clubkick"

d1 $ off 0.5 (id) $ s "clubkick"
```

Making minor alterations to the above examples (adding new samples, patterning our arguments), we explore the nuances of each function and the unique capabilities each possess

```
d1 $ s "clubkick*4 cp*2"

d1 $ fast "4 2" $ s "clubkick cp"

d1 $ ply "4 2" $ s "clubkick cp"

d1 $ every 4 (fast 4) $ every 2 (ply 2) $ fastcat [s "clubkick", s "cp"]

d1 $ struct "1111 11" $ s "clubkick cp"

d1 $ struct "1(<4 2>,4 2,<4 2>)" $ s "clubkick cp"

d1 $ struct "<[1*4 1*2] [[1 1 1 1]@4 [1 1]@2] {1*4 1*2}%<4 2>>" $ s "clubkick cp"

d1 $ off 0.5 (fast "<2 4>" . plyWith "4 2" (|* gain 0.92) . (|* squiz 1.5)) $ s "clubkick cp"

```

# Advanced TidalCycles time (you made it this far!)


### melodic and harmonic concepts

```
-- midi syntax is very similar to the sound syntax.
let m1 = sound "midi" # midichan 0

-- making triplets in melodies.
d1 $ n "0 12 4 12*3" # m1 

-- a non-mininotation way to make triplets in melodies.
d1 $ within (0.75, 1) (fast 3) $ n "0 12 4 12" # "superpiano"

let cMajor = [0, 2, 4, 5, 7, 9, 11]

-- toScale function syntax.
-- map patterns of ints to patterns of different ints
d1 $ n (toScale cMajor "0 1 2 3")  # "superpiano"

-- shifting scale degrees
d1 $ n (toScale cMajor ("0 1 2 3" |+ "[2 4 8]/3"))  # "superpiano"

-- shifting chord degrees
d1 $ n (toScale cMajor ("0 1 2 3" |+ "[[0, 2, 4] [1,3,5] [2,4,6]]/3"))  # "superpiano"

-- the meaning of '|+' vs '+|'
d1 $ n (toScale cMajor ("0 1 2 3" +| "[[0, 2, 4] [1,3,5] [2,4,6]]/3"))  # "superpiano"
-- rhythm is completely different!

-- using struct to provide an independent rhythmic context
-- where the harmonic/melodic elements are a blueprint to 'sample' from.
d1 $ struct "t(3,8)" $ n (toScale cMajor ("0 1 2 3" |+ "[2 4 8]/3"))  # "superpiano"

-- another fun idea:  using the new-ish nTake function to SuperCollider's state memory.
d1 $ struct "t(3,8)" $ nTake "myMelody" [0, 2, 4, 9, 2, 4]  # "superpiano"

d1 $ struct "t(3,8)" $ nTake "myMelody" [0, 2, 4, 9, 2, 4, 2, 14]  # "superpiano"

-- cross rhythms happen when nTake list length neither matches nor evenly subdivides
--  the rhythmic length (onsets per cycle)
-- 9 vs 8.
d1 $ slow 2 $ struct "t(9,16)" $ nTake "myMelody" [0, 2, 4, 9, 2, 4, 2, 14]  # "superpiano"

d2 $ s "bd hh cp hh"

-- simpler cross rhythm example:  3 vs 4
d1 $ struct "t(3,8)" $ nTake "myMelody" [0,2,4,9]  # "superpiano"
```

### Rhythmic manipulation

```
-- shifting time
d1 $ (0.25 <~) $ s "bd hh cp hh"

-- patterning <~
d1 $ ("0 0 0.1 0" <~) $ s "bd hh cp hh"
-- what happened to the clap?

d1 $ ("0 0 0.25 0" <~) $ s "bd hh cp hh"

-- overshooting 0.25
d1 $ ("0 0 0.3 0" <~) $ s "bd hh cp hh"

d1 $ ("0.25 0.5 0.85 0.5 0.125" <~) $ s "bd hh cp hh"

-- an easier way to achieve a swing is with a different method altogether, using the nudge function
d1 $ s "bd hh cp hh"  # nudge "[0 0.125]*2"

-- is almost the same as
d1 $ ("[0 0.125]*2" ~>) $ s "bd hh cp hh"

d1 $ ("[0 0.125]*2" ~>) $ s "bd hh cp hh"

-- vs the simpler
d1 $ "bd hh cp hh"  # nudge "[0 0.125]*2"

-- nudge on the 16th notes for a classic house swing
d1 $ (# nudge "[0 0.015]*8") $ s "bd hh ho hh [bd, cp] hh ho hh bd hh ho hh [bd, cp] hh ho hh"

-- how can we do more complex, "algorithm-only" things?
d1 $ (fast "1.125 1 1.125 1") $ s "bd hh cp hh"

d1 $ (fast "2 1 2 1") $ s "bd hh cp hh"

-- a first attempt at using a sine wave to modulate the tempo
d1 $ (fast sine) $ s "bd hh cp hh"

d1 $ (fast sine) $ s "bd hh ho hh [bd, cp] hh ho hh bd hh ho hh [bd, cp] hh ho hh"

-- lets try fixing the range
d1 $ (fast (range 1 2 sine)) $ s "bd hh cp hh"

d1 $ (fast (range 1 2 sine)) $ s "bd hh ho hh [bd, cp] hh ho hh bd hh ho hh [bd, cp] hh ho hh"

-- any kind of mathematical function can be used to modulate tempo (and lots of other things!)
d1 $ (fast ((fast (range 0.25 0.75 sine)) $ range 1 2 sine)) $ s "bd hh ho hh [bd, cp] hh ho hh bd hh ho hh [bd, cp] hh ho hh"


d1 $ (fast (range 1 2 sine)) $ s "bd hh cp hh"

d1 $ (fast ((fast (range 0.25 0.75 sine)) $ range 1 2 sine)) $ s "bd hh ho hh [bd, cp] hh ho hh bd hh ho hh [bd, cp] hh ho hh"

-- patterning the tempo

-- continuous functions to modulate tempo
d1 $ s "bd hh cp hh" # cps (slow 8 $ 0.5 + saw)

d1 $ s "bd hh cp hh" # cps (slow 8 $ range 1 2 sine)

-- discrete tempo changes with segment
d1 $ s "bd hh cp hh" # cps (segment 2 ( 0.5 + saw))

d1 $ (slow 4 $ s "bd hh cp hh") # cps (segment 2 ( 0.5 + saw))

d1 $  s "bd hh cp hh" # cps (slow 4 $ segment 2 ( 0.5 + saw))
```