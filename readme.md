# Inline Music
*Inline Music* is a simple plain-text format for musical notation. It is intended mostly
for short phrases of western music. I looks like this:

```
# This is a comment.
[author Pelle Juul] # This is a "command" naming the author.
[meter 4/4]         # Another command specifying that this piece is in 4/4 time.
[tempo 1/4 120]     # It's tempo is 120 1/4 beats per minute.

# A stave with a melody and barlines,
{ C E G2  | (c e)/2 G E C | C+4 ||}
# A stave with chords, one octave below, without barlines.
{ <CEG>_4 <EGc>_4 <Gce>+_2 <CEG>+2 }
```

Without comments, spaces, and newlines it can be pretty space efficient, suitable for web applications:

```
[author Pelle Juul][meter 4/4][tempo 1/4 120]{CEG2(ce)/2GECC+4}{<CEG>_4<EGc>_4<Gce>+_2<CEG>+2}
```

The remainder of this document describes the format in further detail.

## Pitch
The musical pitches are simply named by their corresponding letter:

```
C D E F G A c d e f g a b
```

`C` is middle c, `c` is an octave above. For higher and lower octaves, a caret `^` or underscore `_` is added:

```
C_ D_ E_ F_ G_ A_ B_ C D E F G A B c d e f g a b c^ d^ e^ f^ g^ a^ b^ c^
```

A pitch can be made sharp or flat by adding `+` or `-` after the pitch name, e.g. `C+` for C# and `A-_` for Ab below middle c. Unlike standard notation, accidentals only apply to a single note. So when we write `A+ A` it is just A# A, while it would be A# A# in standard notation. For simplicity, and since accidentals only apply to a single note, the "natural" accidental is not included. 

## Duration
When a pitch has a musical duration, it is a note. The default duration of a note is 1/4. So, if we write just `C` it will be a 1/4 note with the pitch of `C`.

Longer notes are notated by adding a multiplier after the note. So, if we write `C2` we will have a note of 1/2, and if we write `C4` we will have a note of 1 - a full bar. 

Shorter notes are notated by adding a slash `/` followed by a divisor. So, if we write `C/2` we will have an 1/8 note. `C/4` will give us an 1/16 note.

Like traditional sheet music we can "dot" a note, multiplying the duration by 1.5. So `C/2.` will be a dotted eight note.

A duration is always notated after the pitch. For example would a dotted eight note on C below middle C will be written as `C_/2.`.

## Rests
Rests are notated like pitches, but with the letter `R`. Rests are case insensitive, so `r` is also valid. Like notes, rests uses the default duration. Duration modifiers are applied the same way, so `r2` is a 2/4 rest, and `r/2` is a 1/8 rest. Pitch modifiers like `_`, `^` , and `+` does not affect rests.

## Grouping
Musical pieces of contain sequences of notes in the same octave and/or with the same duration [citation needed]. To avoid having to write something like:

```
C_/2 E-_/2 G_/2 C/2
```

we can use a shorthand by writing the notes inside parentheses `()`, and apply the modifiers to the group. So, we can rewrite the music above as

```
(C E- G c)_/2
```

Groups can also contain rests:

```
(C E- G R)_/2
```

## Note on Reading
The way the computer reads inline music can be easily understood by translating modifiers into word. Let's take `C+_/2`; this is read as

* `C`: A 1/4 note on middle C.
* `+`: Transpose the pitch up one semitone.
* `_`: Transpose the pitch down an octave.
* `/2`: Divide duration by two.

For completeness, another example could be `(eg)-^4`, which is read as

* `(eg)`: A sequence of two 14 notes, E and G in the fifth octave.
* `-`: Transpose the pitch of each note down one semitone.
* `^`: Transpose the pitch of each note up one octave.
* `4`: Multiply the duration of each note by four.

## Staves

A stave contains series of musical notes. The beginning and end of a stave is notated with `{` and `}`. For example 

```
{ C E G2 }
```

is a stave containing an arpeggio of a C-Major chord. White space is generally between notes are ignored, so writing `{CEG2}` would be just as valid.

There can be multiple staves in a piece of inline music:

```
{ C  E G2}
{ C_4 }
```

is the same arpeggio as before, but with an added bass note on another stave. It is also possible to notate harmonies by enclosing notes with `<` and `>`, for example

```
{ C E G2  }
{ <CEG>_4 }
```

is a C-Major arpeggio with a C-Major chord below. Notice that modifiers are applied to harmonies like they are to groups.

## Bar Lines

Bar lines are written with the pipe character `|`. Bar lines are only included for human readability and is ignored by the computer. So, a to bar piece of music could be written like:

```
{ C E G2 | G E C2 }
```

Since bar lines are ignored we can also include beginning and ending bar lines:

```
{ | C E G2 | G E C2 || }
```

## Commands

A command contains extra information about how to interpret a stave. A command is written inside square brackets `[]` and consists of a keyword, possibly followed by one or more arguments. Commands are written outside staves and applies to all following staves. An example of commands is:

```
[title Two bar arpeggio on a C-Major chord]
[author Pelle Juul]
[date 2017-01-08]
[meter 4/4]
[tempo 120]
[key F+]
{ | C E G2 | G E C2 | F+ G3 || }
```

The following sections list the basic commands. A specific implementation of inline music may offer custom commands, but these are required. Unrecognized commands should be ignored.

### Metadata
Metadata commands add information to the music, which doesn't have anything to do with the actual music.

```
[author <name>]
```
The author of the music.

```
[date <date>]
``` 
The date the music was written, in [ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601).

```
[title <name>]
``` 
The title of the music.

### Notation
Notation commands modify the way the music is interpreted.

```
[key <modifiers>]
```

The key of the music. Works much like a key signature in standard notation. The modifiers contains notes which should be sharpened or flattened. So if we want G major we would write `[key F+]`, to indicate that every time we write `F` we actually mean `F+`. If we wanted Eb major we would write `[key A-B-E-]`. This notation also allows for non-standard key signatures, for example we could write `[key G+]` for an A harmonic minor scale. No modifiers means that we´re playing without accidentals - C major and it's modes.

```
[note <value>]
``` 
The default duration of a note. For some music, a standard note length of 1/4 may not be suitable. To avoid having to read and write duration modifiers all the time, the default duration may be changed. E.g. `[note 1/8]` or `[note 1/16]`

### Performance and Rendering
```
[meter <meter> <stress>]
```
The meter or time signature of the music. Like standard notation it shows how many notes there are in a bar, and implicitly which notes are stressed. Examples are `[meter 4/4]`, `[meter 2/4]`, `[meter 3/4]`, and `[meter 6/8]`. The second argument is an optional stress pattern, for example `[meter 5/4 3+2]` means that a notes one and four are stressed (the bar is counted as *one*-two-three-*one*-two).

```
[tempo <note> <bpm>]
```
Sets the tempo of the music, which is used for playback or when converting to other formats. It works much like a metronome mark in standard notation, so `[tempo 1/4 120]` means that we have 120 1/4 notes per minute.

## Comments
Inline music can contain comments. A comment starts with a `#` and spans the rest of the line:

```
# This is a piece with two staves and three comments
{ C E G c } # First voice
{ C C E G } # Second voice
```

## Tools
To encourage use of inline music, some tools should be created. Some ideas are:

* *Sheet music generator:* should generate SVG or PNG images from inline music.
* *MIDI converter:* should convert inline music to and from MIDI files.
* *JavaScript parser/generator:* should enable developers to parse and generate inline music.
* *Command line utility* should be able to perform common oprations such a minifying, prettyfying, and transposition.
* *Sheet music web API* for including inline music notated sheet music in blogs and websites.
* *Webplayer:* a HTML/JavaScipt component for inline music playback. 
* *Writer web app:* a web app for writing inline music in a user-friendly fashion.
