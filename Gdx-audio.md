**This extension has been removed as per 8th of April 2014**

Gdx-audio is an extension offering a lower-level API for audio. Features are:
* Decoders for mp3, ogg Vorbis and wav, using Mpg123, Xiph Tremor
* KissFFT and Java FFT by Damien Di Fede for comparison
* SoundTouch for pitch shifting, time stretching and playback rate modification

It is being distributed with every release in the extensions folder. To use the extension add the gdx-audio.jar and gdx-audio-natives.jar to your desktop project. For your android project add the gdx-audio.jar and copy the libgdx-audio.so files to your libs/armeabi and libs/armeabi-v7a folders. GWT (html) and iOS backends are currently not supported.

For usage examples see:
* Mpg123Test, shows how to decode an mp3 with the Mpg123Decoder class
* VorbisTest, shows how to decode an ogg with the VorbisDecoder class
* WavTest, shows how to decode an wav with the WavDecoder class
* SoundTouchTest, shows how to apply pitch shifting to a PCM stream

Caveat: The vorbis and mp3 decoder can only decode files stored on the external storage. There might be the possibility to work around that limitation in the future. For most practical purposes it shouldn’t be too limiting.