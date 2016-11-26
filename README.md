# LipLab

Free lip sync / automatic phoneme extraction tool for game developers. Currently only for Windows 10+ only due to the voice recognition API, sorry. (Let me know about any decent open source multiplatform speech recognition libraries that can provide phoneme timings!)

## DEVELOPMENT

Sooo this doesn't actually exist yet. I've written out a spec that should theoretically work. All the individual parts of the toolchain are there, it's just a matter of spending a good month trying to connect everything and making it usable / stable. I don't have a lot of time, to be honest, but eventually I want to build this out. If someone else wants to implement my spec and stuff, I'd be grateful... whatever you do, it should NOT be a Unity Editor plug-in, but rather a standalone tool, so that anyone with any game engine could make use of it.


## QUICKSTART

1. CONFIGURE WINDOWS. Enable Speech Recognition, and enable Stereo Mix and set it to primary microphone. This is so Windows will automatically try to listen to your computer's speaker audio. Make sure you crank the volume up too.

2. START LIPLAB.

3. TELL LIPLAB WHERE YOUR SOUNDS ARE. Point LipLab to the file folder where all your voiceover files are. LipLab will automatically crawl all subfolders and load the files into a list.

4. CONFIGURE LIPLAB. Use simplified phoneme set (good for cartoony characters) or full phoneme set (good for realistic characters)?

5. PROCESS. You can click "Batch Process" to have LipLab automatically process every file, or manually select a file from the file list to process it individually. When you process a file, LipLab runs it through several passes (see "How It Works") to try to figure out how to pronounce it.

6. CORRECT. Phoneme extraction is not perfect, you'll usually have to correct the phonemes and the timings. There's a 3D preview of a character who will use the work-in-progress data to lipsync to your audio; use the draggable timeline interface to change phoneme times / durations, or even manually input all the phonemes yourself!

7. EXPORT DATA. LipLab will export a giant JSON file that contains all your lipsync and phoneme data for that entire folder.

8. IMPORT INTO AN INTEGRATION FOR YOUR GAME ENGINE. Your game engine needs to know how to parse the JSON and animate characters with the phoneme data. There's an integration for Unity included, but for other engines, it's up to you.


## HOW IT WORKS

LipLab uses the Windows 10 Speech Recognizer that Unity can access automatically. (UnityEngine.Windows.Speech)

First, it listens to your audio through a DictationRecognizer that spits out text. Then it listens to your audio again, but this time, it listens for each word with a KeywordRecognizer -- this gives us timings and durations for each word.

Once we have the words and the timings, we run each word through the CMU Text-To-Phoneme dictionary Phoneme guesser https://github.com/cmusphinx/cmudict/blob/master/cmudict.dict (or if it's not the dictionary, we make our best guess using generalized pronounciation rules https://github.com/bashrc/mindpix/blob/master/mpcreate/phoneme.cs) and then slot the phonemes within the timings for each word.

You can fine tune the timings on the timeline: drag the phonemes around, or left-click on an empty area to add a new phoneme, or right-click to delete a phoneme. You'll also see a 3D preview of a character lip syncing along with the audio. 

At the end, we spit out a JSON file that looks like this:

<pre>lipsync : [
	{
		"filename":"male01/voiceover_test.wav"
		"dictation":"test"
		"phonemes":[
			{
			"0.24-0.56":"T"
			"0.56-1.21":"S"
			"1.21-1.51":"T"
			}
		]
	}
]</pre>

For the Unity integration, LipLabManager.cs will parse the JSON file, and during the game, it will feed phoneme data to a LipLabSpeaker.cs script component on your game character, which is already configured to work with the default Mixamo Fuse characters' BlendShape setup.
