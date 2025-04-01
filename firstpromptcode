// --- blind_test.js ---

document.addEventListener('DOMContentLoaded', () => {
    // --- Configuration ---
    const snippetDuration = 10000; // Duration to play snippet in milliseconds (e.g., 10000 = 10 seconds)

    // --- Song Data ---
    // Using examples from Wikimedia Commons (Public Domain / Creative Commons)
    // IMPORTANT: Verify licenses and consider hosting these files yourself for reliability.
    const songs = [
        {
            url: 'https://upload.wikimedia.org/wikipedia/commons/c/c7/Beethoven_-_F%C3%BCr_Elise.ogg',
            // Acceptable answers (case-insensitive)
            answers: ['für elise', 'fur elise', 'beethoven fur elise', 'beethoven für elise'],
            title: 'Für Elise',
            artist: 'Ludwig van Beethoven'
        },
        {
            url: 'https://upload.wikimedia.org/wikipedia/commons/a/a6/W.A._Mozart_-_Rondo_alla_Turca.ogg',
            answers: ['rondo alla turca', 'turkish march', 'mozart rondo alla turca', 'mozart turkish march'],
            title: 'Rondo alla Turca (Turkish March)',
            artist: 'Wolfgang Amadeus Mozart'
        },
        {
            url: 'https://upload.wikimedia.org/wikipedia/commons/8/89/Bach_-_Toccata_and_Fugue_in_D_minor_BWV_565.ogg',
            answers: ['toccata and fugue in d minor', 'bach toccata and fugue', 'toccata and fugue'],
            title: 'Toccata and Fugue in D minor, BWV 565',
            artist: 'Johann Sebastian Bach'
        },
        {
            url: 'https://upload.wikimedia.org/wikipedia/commons/7/73/United_States_Army_Band_-_Pachelbel%27s_Canon.ogg',
            answers: ["pachelbel's canon", "canon in d", "pachelbel canon", "pachelbel canon in d"],
            title: "Pachelbel's Canon (Canon in D Major)",
            artist: 'Johann Pachelbel'
        }
        // Add more songs here following the same structure
    ];

    // --- Game State Variables ---
    let currentSongIndex = -1; // Start at -1 so the first load goes to 0
    let score = 0;
    let audioPlayer = null; // Will hold the Audio object
    let snippetTimeout = null; // To store the timeout ID for stopping the snippet
    let availableSongs = [...songs]; // Copy of songs to shuffle and pick from

    // --- DOM Element References ---
    // Ensure these IDs exist in your HTML file
    const playButton = document.getElementById('playButton');
    const guessInput = document.getElementById('guessInput');
    const submitGuessButton = document.getElementById('submitGuess');
    const nextButton = document.getElementById('nextButton');
    const feedbackDiv = document.getElementById('feedback');
    const scoreDisplay = document.getElementById('scoreDisplay');
    const songInfoDiv = document.getElementById('songInfo'); // Optional: To show correct answer
    const audioPlayerElement = document.getElementById('audioPlayer'); // Get the <audio> tag

    // --- Core Functions ---

    // Shuffle array randomly (Fisher-Yates algorithm)
    function shuffleArray(array) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]]; // Swap elements
        }
    }

    // Load the next song
    function loadNextSong() {
        // Stop any currently playing audio and clear timeout
        if (audioPlayer) {
            audioPlayer.pause();
            audioPlayer.currentTime = 0;
        }
        if (snippetTimeout) {
            clearTimeout(snippetTimeout);
            snippetTimeout = null;
        }

        currentSongIndex++;

        if (currentSongIndex >= availableSongs.length) {
            // Game Over
            feedbackDiv.textContent = `Game Over! Final Score: ${score} / ${availableSongs.length}`;
            playButton.disabled = true;
            guessInput.disabled = true;
            submitGuessButton.disabled = true;
            nextButton.disabled = true;
            songInfoDiv.textContent = "Thanks for playing!";
            return;
        }

        // Reset UI for the new song
        const currentSong = availableSongs[currentSongIndex];
        audioPlayerElement.src = currentSong.url; // Set source on the HTML element
        audioPlayer = audioPlayerElement; // Use the existing element

        feedbackDiv.textContent = '';
        songInfoDiv.textContent = ''; // Clear previous song info
        guessInput.value = '';
        guessInput.disabled = false;
        submitGuessButton.disabled = false;
        playButton.disabled = false;
        nextButton.style.display = 'none'; // Hide next button until guess is submitted


        // Preload audio metadata (optional, helps ensure duration is known)
        audioPlayer.load();

        // Optional: Log loading state
        audioPlayer.oncanplaythrough = () => {
            console.log(`Song ${currentSongIndex + 1} ready to play.`);
        };
        audioPlayer.onerror = (e) => {
            console.error("Error loading audio:", e);
            feedbackDiv.textContent = "Error loading audio. Trying next song.";
            // Maybe try to skip to the next song automatically or provide a manual skip
             setTimeout(loadNextSong, 2000); // Attempt to load next after delay
        };
    }

    // Play the snippet
    function playSnippet() {
        if (!audioPlayer || !audioPlayer.src || audioPlayer.readyState < 2) { // Check if audio is loaded enough
             feedbackDiv.textContent = "Audio not ready yet. Please wait.";
             console.warn("Audio not ready or src missing.");
             // Attempt to load again if src is missing but should exist
             if (availableSongs[currentSongIndex] && !audioPlayer.src) {
                audioPlayer.src = availableSongs[currentSongIndex].url;
                audioPlayer.load();
             }
             return;
        }

        audioPlayer.currentTime = 0; // Start from the beginning
        audioPlayer.play()
            .then(() => {
                playButton.disabled = true; // Disable play button while snippet plays
                 // Stop playback after snippetDuration
                snippetTimeout = setTimeout(() => {
                    if (audioPlayer) {
                        audioPlayer.pause();
                        playButton.disabled = false; // Re-enable play button
                    }
                }, snippetDuration);
            })
            .catch(error => {
                console.error("Error playing audio:", error);
                feedbackDiv.textContent = "Could not play audio.";
                playButton.disabled = false; // Re-enable button on error
            });
    }

    // Check the user's guess
    function checkGuess() {
        if (currentSongIndex < 0 || currentSongIndex >= availableSongs.length) return; // No song loaded

        const currentSong = availableSongs[currentSongIndex];
        const userGuess = guessInput.value.trim().toLowerCase();

        if (!userGuess) {
            feedbackDiv.textContent = "Please enter a guess.";
            return;
        }

        let isCorrect = false;
        // Check if userGuess matches any of the acceptable answers
        if (currentSong.answers.includes(userGuess)) {
            isCorrect = true;
        }

        // Provide feedback
        if (isCorrect) {
            feedbackDiv.textContent = "Correct!";
            feedbackDiv.style.color = 'green';
            score++;
            scoreDisplay.textContent = `Score: ${score}`;
        } else {
            feedbackDiv.textContent = "Incorrect!";
            feedbackDiv.style.color = 'red';
        }

        // Show the correct answer
        songInfoDiv.textContent = `The song was: ${currentSong.title} by ${currentSong.artist}`;

        // Disable guessing and show 'Next' button
        guessInput.disabled = true;
        submitGuessButton.disabled = true;
        playButton.disabled = true; // Disable playing again after guessing
        nextButton.style.display = 'inline-block'; // Show the next button

         // Stop audio if it's still playing
        if (audioPlayer) {
            audioPlayer.pause();
        }
        if (snippetTimeout) {
            clearTimeout(snippetTimeout);
            snippetTimeout = null;
        }
    }

    // --- Event Listeners ---
    playButton.addEventListener('click', playSnippet);
    submitGuessButton.addEventListener('click', checkGuess);
    nextButton.addEventListener('click', loadNextSong);

    // Optional: Allow submitting guess with Enter key in the input field
    guessInput.addEventListener('keypress', (event) => {
        if (event.key === 'Enter' && !submitGuessButton.disabled) {
            checkGuess();
        }
    });

    // --- Initialization ---
    function initializeGame() {
        shuffleArray(availableSongs); // Randomize song order
        score = 0;
        currentSongIndex = -1;
        scoreDisplay.textContent = `Score: ${score}`;
        nextButton.style.display = 'none'; // Hide next button initially
        guessInput.disabled = true; // Disable input until first song loaded
        submitGuessButton.disabled = true; // Disable submit until first song loaded
        playButton.disabled = true; // Disable play until first song loaded
        feedbackDiv.textContent = "Loading game...";
        songInfoDiv.textContent = "";

        // Create the audio element dynamically if not present, or ensure the one in HTML is ready
         if (!audioPlayerElement) {
             console.error("CRITICAL: HTML must contain an <audio> element with id='audioPlayer'");
             feedbackDiv.textContent = "Error: Missing audio player element in HTML.";
             return;
         }
         audioPlayer = audioPlayerElement; // Assign the HTML element
         audioPlayer.preload = 'metadata'; // Helps get duration etc. faster

        // Load the first song
        loadNextSong();
        feedbackDiv.textContent = "Game ready! Press Play."; // Update status
    }

    initializeGame(); // Start the game when the DOM is ready

}); // End of DOMContentLoaded
