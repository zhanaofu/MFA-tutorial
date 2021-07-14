# Batch processing

As specified in the official document, when many recordings are being aligned at the same time, they should be organized as below. Note that recordings from different speakers should be kept in separate folders. This is because the alignment adapts to each speaker's characteristics.

```
+-- textgrid_corpus_directory
|       --- recording1.wav
|       --- recording1.TextGrid
|       --- recording2.wav
|       --- recording2.TextGrid
|       --- ...
+-- prosodylab_corpus_directory
|   +-- speaker1
|           --- recording1.wav
|           --- recording1.lab
|           --- recording2.wav
|           --- recording2.lab
|   +-- speaker2
|           --- recording3.wav
|           --- recording3.lab
|       --- ...
```

In the following section I'll show some short scripts that could be helpful file preparation in batch.

## 1. Transcribing recordings for recordings with different content with DeepSpeech

This script assumes the recordings are kept in separate folders (for different speakers) under the 'audio' directory and output the transcript to a .txt file with the same name as the audio.

```bash
for file in audio/*/*.wav; do
  deepspeech --model deepspeech-0.9.3-models.pbmm --scorer deepspeech-0.9.3-models.scorer --audio $file > ${tmp/wav/txt};
done
```

## 2. Organizing audio and transcripts in the desired structure

Each audio needs its own transcript. In phonetic experiments, the transcripts are usually the same for many recordings. For example, I have some recordings organized as below:

```
/users/f/downloads/shared/demo/audio/
├── JP_sentences
│   ├── Sentence1_1.wav
│   ├── Sentence1_2.wav
│   ├── Sentence1_3.wav
│   ├── Sentence1_4.wav
│   ...
└── SP_sentences
    ├── Sentence\ 1_1.wav
    ├── Sentence\ 1_2.wav
    ├── Sentence\ 1_3.wav
    ├── Sentence\ 1_4.wav
    ...
```

Each speaker recorded 5 different sentences with each repeated many times. I have the transcripts for all sentences in another folder 'text'. So I can use the following Python script to create transcripts according to the audio's file name and the corresponding transcripts (if you save the script as 'file_prep.py', run it with the command ```python file_prep.py```):

```python
import os, re, shutil
from glob import glob

data_directory = '/Users/f/Downloads/shared/demo/audio'
txt_directory = '/Users/f/Downloads/shared/demo/text'

os.chdir(data_directory)

# Cycling through all speakers
for folder in glob(data_directory+'/*/'):
	print(folder)

  # Copying the audio and the transcripts to another directory
  # This keeps the original audio unchanged but it's not necessary
	outd = folder.replace('audio','process')
	os.makedirs(outd, exist_ok=True)

  # Cycling through all audio for each speaker
  for wav in glob(folder+'*.wav'):

    # Replacing the spaces in audio files' names with '_'
    # and get the output directory
		wav2 = wav.replace(' ','_').replace('audio','process')

    # get the sentence number (the first number before '_')
		sentence_n = re.findall('[0-9](?=_)',wav2)[0]

    shutil.copyfile(wav, wav2)
		shutil.copyfile(txt_directory+'/'+sentence_n+'.txt',wav2.replace('wav','txt'))
```

After running the script, the organized files can be found under the 'process' directory:

```
/users/f/downloads/shared/demo/process/
├── JP_sentences
│   ├── Sentence1_1.txt
│   ├── Sentence1_1.wav
│   ├── Sentence1_2.txt
│   ├── Sentence1_2.wav
│   ...
└── SP_sentences
    ├── Sentence_1_1.txt
    ├── Sentence_1_1.wav
    ├── Sentence_1_2.txt
    ├── Sentence_1_2.wav
    ...
```

Now we can run the alignment command. Then the aligned .textgrid files should appear under the 'transcriptions' directory.

```bash
mfa align /users/f/downloads/shared/demo/process /Users/f/Documents/MFA/pretrained_models/dictionary/librispeech-lexicon.txt english /users/f/downloads/shared/demo/transcriptions --clean
```
```
/users/f/downloads/shared/demo/transcriptions/
├── JP_sentences
│   ├── JP-sentences-Sentence1-1.TextGrid
│   ├── JP-sentences-Sentence1-2.TextGrid
│   ├── JP-sentences-Sentence1-3.TextGrid
│   ├── JP-sentences-Sentence1-4.TextGrid
│   ...
└── SP_sentences
    ├── SP-sentences-Sentence-1-1.TextGrid
    ├── SP-sentences-Sentence-1-2.TextGrid
    ├── SP-sentences-Sentence-1-3.TextGrid
    ├── SP-sentences-Sentence-1-4.TextGrid
    ...
```

[Last update: Jul 14, 2021]
