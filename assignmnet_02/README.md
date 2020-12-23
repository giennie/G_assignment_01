#The outcome of the major project was not eventually made using Python.
#After consulting with major studio leaders Salome and American, I produced Final Outcome music from my own recordings.
#But for this Python project, I used a library called pyknon to create random music.

#I'm still not familiar with the Python program,
#so I originally coded in a way that music comes out when I play the saved midi file
#using a program called an online sequencer, but just before the submission, the program had trouble
#and tried to take a video of the process, but failed. I'm so sorry for this.
#I'm still wandering in the world of coding.
#Fortunately, however, I have the outcom music I saved earlier, so I'll submit the mp3 file together.

from pyknon.genmidi import Midi
from pyknon.music import NoteSeq, Note, Rest

#I am goig to make random music 
import random

#Create Demo mid
#Online sequencer (https://onlinesequencer.net) only works properly on Chrome brower

notes1 = NoteSeq('D4 F38 A Bb4')
midi = Midi(number_tracks=1, tempo=0)
midi.seq_notes(notes1, track=0)
midi.write('examples/demo.mid')

#Generate Random Notes Function

#it will be faster when the note will get closer to the 1/16 from 1/2
durations = [
    1/2, # half note
    1/4, # quarter note
    1/8, # eighth note
    1/16 # sixteenth note
]

#from C to B (12 notes)
'''
0 1 2 3 4 5 5 6 7 8 9 10 11
C C# D D# E F F# G G# A A# B
'''

#I justified the A,G,D major chord
# A_major = ['A','B','C#','D','E','F#','G#']
A_major = [9, 11, 1, 2, 4, 6, 8]
# G_major = ['G','A','B','C','D','E','F#']
G_major = [7, 9, 11, 0, 2, 4, 6]
# D_major = ['D','E','F#','G','A','B','C#']
D_major = [2, 4, 6, 7, 9, 11, 1]

#if I set the rests as True, that means there might be no sound in the note as a random
#n means a number of the notes
def get_random_notes(n, pitches, durations, rests=True):
    if rests:
        pitches.append('r')
        
    resutls = NoteSeq()

    for i in range(n):
        pitch = random.choice(pitches)
        duration = random.choice(durations)

        if pitch == 'r':
            #class Rest is = no music(stop music) during the duration
            result.append(Rest(dur=duration))
        else:
            #I set the octave as 4 
            result.append(Note(pitch, octave=4, dur=duration))

    return result


#Generate Random Sound

notes1 = get_random_notes(20, pitches=A_major, durations=[1/8, 1/16])
midi = Midi(number_tracks=1, tempo=120)
midi.seq_notes(notes1, track=0)
midi.write('examples/A_major.mid')

print(notes1)

#Percussion
#for the drum, I am not using random, I set the hihat & kicknare
#*16 means, it repeats 16 times
hithat = NoteSeq('F#16,, R16') * 16
kick_snare = NoteSeq('C16,, R16 R16 C16 D16 R16 R16') * 4

#when I save the notes, set two track=0 as same value, it save as one same track
midi = Midi(number_tracks=1, tempo=120)
midi.seq_notes(hithat, track=0)
midi.seq_notes(kick_snare, track=0)
midi.write('examples/drum.mid')

#Make it Together
#I am going to save the drum and melody track, so save number_tracks as 2
midi = Midi(number_tracks=2, tempo=120)
midi.seq_notes(hithat, track=0)

#Add Piano
piano = NoteSeq('A4, R4')*4

midi = Midi(number_tracks=3, tempo=120)
midi.seq_notes(hithat, track=0)
midi.seq_notes(kick_snare, track=0)
midi.seq_notes(notes1, track=1)
midi.seq_notes(piano, track=2)
midi.write('examples/Amajor_drum_piano.mid')

