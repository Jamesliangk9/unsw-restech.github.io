title: Multimodal Models with Ollama

<h1>Using Multimodal Models with Ollama on Katana</h1>

Some Ollama models can work with more than just text.  
These are known as **multimodal models** and can analyse:

- images
- screenshots
- diagrams
- charts
- scientific figures
- photos

Audio workflows are also possible by combining Ollama with speech-to-text tools such as Whisper.

This page explains how to use multimodal models on Katana.

---

<h2>What is a Multimodal Model?</h2>

A multimodal model can process multiple types of input instead of only text.

For example:

| Input Type | Example |
|---|---|
| Text | prompts and questions |
| Images | PNG, JPG screenshots or photos |
| Audio (indirectly) | speech converted into text |

Examples of multimodal Ollama models:

```text
llava
llama3.2-vision
bakllava
moondream
```

---

<h1>1. Start an Interactive GPU Job</h1>

Vision models are significantly faster on GPUs.

Start an interactive GPU session:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1
```

After the job starts you should see something similar to:

```text
z1234567@k001:~
```

This means you are now on compute node `k001`.

---

<h2>Choosing a GPU</h2>

Katana provides several different GPU models. Depending on the model you choose, performance can vary significantly, especially for larger language models and multimodal models.

<h3>View Available GPU Nodes</h3>

To see which GPU nodes are currently available:

```bash
pstat --gpu
```

Example output:

```text
Node   GPU Model   Status
k206   H200        Free
k207   A100        Busy
k208   V100        Free
```

<h3>View Detailed Information About a Node</h3>

If you want to inspect a particular node, use:

```bash
pbsnodes k206
```

This displays information such as:

- GPU model
- Number of GPUs
- CPU count
- Memory
- Current jobs running on the node

<h3>Request a Specific GPU Model</h3>

When starting an interactive job, you can request a specific GPU type.

Example:

```bash
qsub -I -l select=1:gpu_model=H200
```

You can also request additional resources:

```bash
qsub -I -l select=1:ncpus=8:mem=64gb:ngpus=1:gpu_model=H200
```

<h3>Recommendations for Ollama</h3>

| Model | Recommended Hardware |
|---------|---------|
| phi3 | CPU or any GPU |
| gemma:2b | CPU or any GPU |
| llama3:8b | A100 or better |
| mistral:7b | A100 or better |
| llava | GPU recommended |
| llama3.2-vision | GPU strongly recommended |
| llama3:70b | H100/H200 recommended |
| mixtral | H100/H200 recommended |

!!! tip
    If you are learning Ollama for the first time, start with `phi3` or `llama3:8b`. These models are smaller, download quickly, and are ideal for testing your workflow before moving to larger models.


<h1>2. Load the Ollama Module</h1>

```bash
module load ollama
```

Verify that Ollama is available:

```bash
ollama --version
```

You may see:

```text
Warning: could not connect to a running Ollama instance
Warning: client version is 0.17.7
```

This is normal before the Ollama server has been started.

---

<h1>3. Set the Model Storage Location</h1>

Large models should be stored in scratch instead of home.

```bash
export OLLAMA_MODELS=/srv/scratch/$USER/ollama/models
mkdir -p $OLLAMA_MODELS
```

---

<h1>4. Start the Ollama Service</h1>

```bash
ollama serve
```

Expected output:

```text
Listening on 127.0.0.1:11434
```

The terminal is now occupied by the Ollama server.

---

<h1>Recommended Method: Open Another Terminal</h1>

Open another terminal window (CMD / PowerShell / Terminal).

<h2>SSH back into Katana</h2>

```bash
ssh zID@katana.restech.unsw.edu.au
```

Example:

```bash
ssh z1234567@katana.restech.unsw.edu.au
```

<h2>SSH into the same compute node</h2>

If your compute node is:

```text
z1234567@k001
```

then run:

```bash
ssh k001
```

Now both terminals are connected to the same job.

---

<h1>5. Pull a Vision Model</h1>

A good starting model is `llava`.

```bash
ollama pull llava
```

Other vision-capable models:

```text
llama3.2-vision
bakllava
moondream
```

---

<h1>6. Run the Vision Model</h1>

Start the model:

```bash
ollama run llava
```

You can now provide an image path.

Example:

```text
>>> /srv/scratch/z1234567/images/test.png
>>> Describe this image
```

Or directly from the command line:

```bash
ollama run llava "Describe this image: /srv/scratch/$USER/images/test.png"
```

---

<h1>Example Use Cases on Katana</h1>

Vision models are useful for:

- microscopy images
- scientific figures
- chart interpretation
- screenshot analysis
- OCR-style tasks
- diagram explanation
- research workflows

---

<h1>7. Using Audio with Ollama</h1>

Ollama itself does not directly process audio files.

The standard workflow is:

```text
Audio File
↓
Speech-to-Text Model
↓
Text Transcript
↓
Ollama Model
```

Most users use Whisper for transcription.

---

<h1>8. Installing Whisper</h1>

Create and activate a Python virtual environment:

```bash
module load python

python -m venv whisper_env

source whisper_env/bin/activate
```

Install faster-whisper:

```bash
pip install faster-whisper
```

---

<h1>9. Example Audio Transcription</h1>

Create a Python script called `transcribe.py`:

```python
from faster_whisper import WhisperModel

model = WhisperModel("base")

segments, info = model.transcribe("audio.mp3")

for segment in segments:
    print(segment.text)
```

Run it:

```bash
python transcribe.py
```

This will produce a text transcript from the audio file.

---

<h1>10. Using the Transcript with Ollama</h1>

Once the transcript has been generated:

```bash
ollama run phi3
```

Example prompt:

```text
Summarise the following meeting transcript:
[paste transcript]
```

---

<h1>GPU vs CPU Recommendations</h1>

<h2>CPU-Friendly Models</h2>

```text
phi3
gemma:2b
moondream
```

<h2>Recommended GPU Models</h2>

```text
llava
llama3
mistral
llama3.2-vision
```

<h2>Large Models</h2>

```text
llama3:70b
mixtral
```

Large models may require very large GPU memory allocations.

---

<h1>Useful Commands</h1>

List downloaded models:

```bash
ollama list
```

Show running models:

```bash
ollama ps
```

Remove a model:

```bash
ollama rm llava
```

Stop Ollama:

```bash
pkill ollama
```

Check GPU usage:

```bash
nvidia-smi
```

---

<h1>Storage Recommendations</h1>

Image and audio datasets can become very large.

Recommended storage location:

```bash
/srv/scratch/$USER/
```

rather than:

```bash
/home/$USER/
```

---

<h1>Ending the Session</h1>

When finished:

```bash
pkill ollama
exit
```

This releases the compute resources.

---

<h1>Minimal Working Workflow</h1>

Terminal 1:

```bash
qsub -I -l select=1:ncpus=4:mem=32gb:ngpus=1

module load ollama

export OLLAMA_MODELS=/srv/scratch/$USER/ollama/models

ollama serve
```

Terminal 2:

```bash
ssh zID@katana.restech.unsw.edu.au

ssh k001

module load ollama

export OLLAMA_MODELS=/srv/scratch/$USER/ollama/models

ollama pull llava

ollama run llava
```
