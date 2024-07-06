# Audio Recording and Transcription Project

This project demonstrates how to record audio, save it, and transcribe it using OpenAI's Whisper model. It also includes generating responses using the OpenAI GPT-4 API. The project utilizes `ipywebrtc` for web-based audio recording and `pyaudio` for local audio recording.

## Table of Contents
- [Introduction](#introduction)
- [Setup](#setup)
  - [Install Dependencies](#install-dependencies)
  - [Import Libraries](#import-libraries)
- [Audio Recording](#audio-recording)
  - [Web-Based Audio Recording with ipywebrtc](#web-based-audio-recording-with-ipywebrtc)
  - [Local Audio Recording with pyaudio](#local-audio-recording-with-pyaudio)
- [Transcription with Whisper](#transcription-with-whisper)
- [Conclusion](#conclusion)
  - [Summary](#summary)
  - [Future Enhancements](#future-enhancements)

## Introduction

This project demonstrates how to record audio, save it, and transcribe it using OpenAI's Whisper model. It also includes generating responses using the OpenAI GPT-4 API. The project utilizes `ipywebrtc` for web-based audio recording and `pyaudio` for local audio recording.

## Setup

### Install Dependencies

Uncomment and run the following commands to install the necessary libraries:

```bash
# !pip install torchaudio ipywebrtc    
# !pip apt update
# !sudo apt install ffmpeg
# !jupyter nbextension enable --py widgetsnbextension
# !pip install soundfile
# !pip install pydub

### Import Libraries

Import all the necessary libraries for the project.

\`\`\`python
import os
import openai
from openai import OpenAI
import base64
import requests
from ipywebrtc import AudioRecorder, CameraStream
import torchaudio
from IPython.display import Audio, display
import wave
import numpy as np
import io
from pydub import AudioSegment
import ipywidgets as widgets
import pyaudio
import whisper
\`\`\`

## Audio Recording

### Web-Based Audio Recording with ipywebrtc

Initialize the camera and recorder:

\`\`\`python
camera = CameraStream(constraints={'audio': True, 'video': False})
recorder = AudioRecorder(stream=camera)
recorder
\`\`\`

Functions to start and stop recording:

\`\`\`python
def start_recording():
    camera = CameraStream(constraints={'audio': True, 'video': False})
    recorder = AudioRecorder(stream=camera)
    recorder.record()
    return recorder

def stop_and_save_recording(recorder):
    audio_data = recorder.stop()
    audio_segment = AudioSegment.from_file(io.BytesIO(audio_data), format="wav")
    filename = "recorded_audio.wav"
    audio_segment.export(filename, format="wav")
    print(f"Audio saved as {filename}")
    display(Audio(data=audio_data, autoplay=True))
\`\`\`

Create buttons for recording control:

\`\`\`python
start_button = widgets.Button(description="Start Recording")
stop_button = widgets.Button(description="Stop Recording")
recorder = True

def on_start_button_clicked(b):
    global recorder
    recorder = start_recording()
    print("Recording started...")

def on_stop_button_clicked(b):
    global recorder
    if recorder:
        stop_and_save_recording(recorder)
        recorder = None
    else:
        print("No recording in progress...")

start_button.on_click(on_start_button_clicked)
stop_button.on_click(on_stop_button_clicked)
display(start_button, stop_button)
\`\`\`

### Local Audio Recording with pyaudio

Function to record audio:

\`\`\`python
def record_audio():
    FORMAT = pyaudio.paInt16
    CHANNELS = 1
    RATE = 16000
    CHUNK = 1024
    RECORD_SECONDS = 3
    WAVE_OUTPUT_FILENAME = r"C:\\Users\\Dronesh MM\\Downloads\\output.wav"
    audio = pyaudio.PyAudio()
    print("Recording started...")
    stream = audio.open(format=FORMAT, channels=CHANNELS, rate=RATE, input=True, frames_per_buffer=CHUNK, input_device_index=2)
    frames = []
    for _ in range(0, int(RATE / CHUNK * RECORD_SECONDS)):
        data = stream.read(CHUNK)
        frames.append(data)
    stream.stop_stream()
    stream.close()
    audio.terminate()
    waveFile = wave.open(WAVE_OUTPUT_FILENAME, 'wb')
    waveFile.setnchannels(CHANNELS)
    waveFile.setsampwidth(audio.get_sample_size(FORMAT))
    waveFile.setframerate(RATE)
    waveFile.writeframes(b''.join(frames))
    waveFile.close()
    print(f"Recording saved to: {WAVE_OUTPUT_FILENAME}")
\`\`\`

## Transcription with Whisper

Function to transcribe audio:

\`\`\`python
def transcribe_audio(file_path):
    model = whisper.load_model("base")
    audio = whisper.pad_or_trim(whisper.load_audio(file_path))
    transcription = whisper.transcribe(model, audio)["text"]
    return transcription
\`\`\`

## Conclusion

This project showcases how to record audio using different methods, save the recordings, transcribe them using Whisper, and continuously handle audio recordings and transcriptions.

### Summary

This project demonstrates how to record audio, save it, and transcribe it using OpenAI's Whisper model. It utilizes both web-based and local recording methods, integrates with OpenAI's GPT-4 API for response generation, and showcases potential enhancements for future development.

### Future Enhancements

- Improve user interface for recording control.
- Integrate additional audio processing capabilities.
- Support multiple audio formats and languages for transcription.

