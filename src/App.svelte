<script>
    import 'bootstrap/dist/css/bootstrap.min.css';
    import 'bootstrap/dist/js/bootstrap.bundle.min.js';

    // Random Note Generator
    const notes = ['C', 'C#/Db', 'D', 'D#/Eb', 'E', 'F', 'F#/Gb', 'G', 'G#/Ab', 'A', 'A#/Bb', 'B'];
    let randomNote = '';
    
    function generateRandomNote() {
        const randomIndex = Math.floor(Math.random() * notes.length);
        randomNote = notes[randomIndex];
        console.log(randomNote);
    }

    generateRandomNote();
    // Metronome
    let bpm = 60;  // Beats per minute
    let intervalId = null;
    let isPlaying = false;
    let audioContext = null;

    function createClickSound() {
        const oscillator = audioContext.createOscillator();
        const gainNode = audioContext.createGain();
    
        oscillator.connect(gainNode);
        gainNode.connect(audioContext.destination);
    
        oscillator.type = 'sine';
        oscillator.frequency.value = 100;
        oscillator.frequency.setValueAtTime(300, audioContext.currentTime);
        gainNode.gain.setValueAtTime(1, audioContext.currentTime);
    
        oscillator.start();
        oscillator.stop(audioContext.currentTime + 0.1);
    }

    function startMetronome() {
        if (isPlaying) return;
        isPlaying = true;
        audioContext = new (window.AudioContext || window.webkitAudioContext)();
        intervalId = setInterval(() => {
            createClickSound();
        }, (60 / bpm) * 1000);
    }

    function stopMetronome() {
        clearInterval(intervalId);
        isPlaying = false;
        if (audioContext) {
            audioContext.close();
            audioContext = null;
        }
    }

    function toggleMetronome() {
        if (isPlaying) {
            stopMetronome();
        } else {
            startMetronome();
        }
    }

    function increaseBPM() {
        bpm++;
        if (isPlaying) {
            stopMetronome();
            startMetronome();
        }
    }

    function decreaseBPM() {
        bpm--;
        if (isPlaying) {
            stopMetronome();
            startMetronome();
        }
    }

    function jumpBPM(jump) {
        bpm = jump;
        if (isPlaying) {
            stopMetronome();
            startMetronome();
        }
    }
</script>

<div class="container">
    <h1>Musicians Tools</h1>
    <div class="row">
        <div class="col-6 note-generator">
            <div class="card h-100">
                <div class="card-body">
                    <h2>Random Scale/Note</h2>
                    <h3>{randomNote}</h3>
                    <button class="btn btn-success rounded-0" on:click={generateRandomNote}>Random</button>
                    <hr>
                    <div class="notes">
                    {#each notes as note}
                        <button class="btn btn-outline-secondary btn-sm rounded-0" class:active={randomNote === note}>{note}</button>
                    {/each} 
                    </div>
                </div>
            </div>
        </div>
        <div class="col-6 metronome">
            <div class="card h-100">
                <div class="card-body">
                    <h2>Metronome</h2>
                    <h3>{bpm} <span class="form-text">BPM</span></h3>
                    <div class="bpm-controls">
                        <button class="btn btn-secondary rounded-0" on:click={decreaseBPM}>-</button>
                        <button class="btn btn-secondary rounded-0" on:click={increaseBPM}>+</button>
                    </div>
                    <button on:click={toggleMetronome} class="btn btn-success rounded-0">{isPlaying ? 'Stop' : 'Start'}</button>
                    <hr>
                    <div class="btn-group" role="group" aria-label="BPM Jump">
                        <button class="btn btn-secondary" on:click={() => jumpBPM(60)}>60</button>
                        <button class="btn btn-secondary" on:click={() => jumpBPM(100)}>100</button>
                        <button class="btn btn-secondary" on:click={() => jumpBPM(120)}>120</button>
                        <button class="btn btn-secondary" on:click={() => jumpBPM(140)}>140</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    .note-generator button {
        margin:10px 0; 
    }
    .bpm-controls {
        display: flex;
        justify-content: center;
        align-items: center;
        margin-top: 10px;
    }
    .bpm-controls button {
        margin: 5px;
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
    }
    h2 {
        font-size:1.2em;
    }
    h2, h3 {
        margin: 0;
        padding: 10px;
    }
    .note-generator h3 {
        border:1px solid #cccccc;
    }
    h3 span.form-text {
        font-size:.5em;
    }
    .notes button {
        border:0;
    }
    .notes {
        border:1px solid #ccc;
    }
</style>