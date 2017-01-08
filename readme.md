# Inline Music

Inline music is a simple plain-text format for musical notation. It is intended mostly
for short phrases of western music.

## Pitch

The musical pitches are simply named by their corresponding letter:

```
C D E F G A c d e f g a b
```
.

`C` is middle c, `c` is an octave above. For higher and lower octaves, a caret `^` or underscore `_` is added: 

```
C_ D_ E_ F_ G_ A_ B_ C D E F G A B c d e f g a b c^ d^ e^ f^ g^ a^ b^ c^
```
.

A pitch can be made sharp or flat by adding `+` or `-` after the pitch name, e.g. `c+` for C# and `A-_` for Ab below middle c.

## Duration

When a pitch has a musical duration, it is a note. The default duration of a note is 1/4. So, if we write just `C` it will be a 1/4 note with the pitch of `C`.

Longer notes are notated by adding a mulitiplier after the note. So, if we write `C2` we will have a note of 1/2, and if we write `C4` we will have a note of 1 - a full bar. 

Shorter notes are notated by adding a slash `/` followed by a divisor. So, if we write `C/2` we will have an 1/8 note. `C/4` will give us an 1/16 note.

Like traditional sheet music we can "dot" a note, multiplying the duration by 1.5. So `C/2.` will be a dotted eight note.

A duration is always notated after the pitch. For example would a dotted eight note on C below middle C will be written as `C_/2.`.

## Grouping

Musical pieces of contain squences of notes in the same octave and/or with the same duration [citation needed]. To avoid habing to write something like

```
C_/2 E-_/2 G_/2 C/2
```
.

We can use a shorthand by writing the notes inside parentheses `()`, and applying the modifiers to the groupd. So, we can rewrite the music above as

```
(C E- G c)_/2
```
.

## Stave

A stave contains series of musical notes. The beginning and end of a stave is notated with `{` and `}`. For example 

```
{ C E G2 }
```

is a stave containing an arpeggio of a C-Major chord. Whitespace is generally between notes are ignored, so writing `{CEG2}` would be just as valid.

There can be multiple staves in a piece of inline music, for example

```
{ C  E G2}
{ C_4 }
```

is the same arpeggio as before, but with an added bass note on another stave. It is also posiible to notate harmonies by enclosing notes with `<` and `>`, for example

```
{ C E G2  }
{ <CEG>_4 }
```

is a C-Major arpeggio with a C-Major chord below. Notice that modifiers are applied to harmonies like they are to groups.

## Commands

A command contains extra information about how to intrepret a stave. They can appear inside or outside - or both - depending on the command. A command is written inside sqarue brackets `[]` and consists of a keyword, possibly followed by one or more arguments.

[note 1/4]
[meter 4/4]
[tempo 120]
[key F#]
[inst piano]

## Tools

Sheet music generator
MIDI generator
MIDI converter
JavaScript library
Python library
Writer webapp
