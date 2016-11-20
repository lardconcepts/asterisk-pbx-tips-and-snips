### Do not use Gosub or GosubIF if anything can interrupt the subroutine before it returns!

I learnt this the hard way.

A subroutine MUST be balanced, and if a subroutine is aborted before it reaches return, you'll be in all sorts of hell.

An example:

You make a subroutine of some sound files you want to loop through to make an IVR menu.

You call it using Gosub. Someone presses two while the third of 5 tracks is playing.

The stack will then be unbalanced and you'll then be in a potential world of stack overflow and unknown hell.

So keep the gosubs for things where they cannot be interrupted. Playback is fine. Background and ControlPlayback is not. See?

Worth saying it one more time...

### Do not use Gosub or GosubIF if anything can interrupt the routine before it returns!
