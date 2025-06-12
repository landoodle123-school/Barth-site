<!--Welcome to the Barth Site Source Code!-->
<!--It is a legacy Svelte format, and no, I am not migrating it to Svelte 5 syntax.-->
<!---->
<!--Most styles are in app.css.-->
<script>
  import Navbar from './lib/Navbar.svelte';

  // --- DevTools script execution detection ---
const originalEval = window.eval;
window.eval = function(...args) {
  alert('Script execution detected! The page will refresh.');
  setTimeout(() => location.reload(), 100);
  throw new Error('Script execution detected!');
};

const originalConsole = { ...console };
['log', 'warn', 'error', 'info', 'debug'].forEach(method => {
  console[method] = function(...args) {
    if (
      args.some(arg =>
        typeof arg === 'string' &&
        /devtools|tamper|script|hack|eval|injected/i.test(arg)
      )
    ) {
      alert('Suspicious console usage detected! The page will refresh.');
      setTimeout(() => location.reload(), 100);
      throw new Error('Suspicious console usage detected!');
    }
    return originalConsole[method].apply(console, args);
  };
});

  // define vars
  let count = 0;
  let amountGained = 1;
  let clickerCount = 0;
  let clickerCost = 100;
  let multiplierCost = 150;
  let clickerMultiplierCost = 1000;
  let clickerGain = 1;
  let clickerIntervals = [];
  let confirmReset = false;

  // Load state from localStorage
  count = parseInt(localStorage.getItem('count') || "0", 10);
  clickerCount = parseInt(localStorage.getItem('clickerCount') || "0", 10);
  clickerCost = parseInt(localStorage.getItem('clickerCost') || "100", 10);
  multiplierCost = parseInt(localStorage.getItem('multiplierCost') || "150", 10);
  clickerMultiplierCost = parseInt(localStorage.getItem('clickerMultiplierCost') || "1000", 10);
  amountGained = parseInt(localStorage.getItem('amountGained') || "1", 10);
  clickerGain = parseInt(localStorage.getItem('clickerGain') || "1", 10);

  // Save state to localStorage
  function saveState() {
    localStorage.setItem('count', count.toString());
    localStorage.setItem('clickerCount', clickerCount.toString());
    localStorage.setItem('clickerCost', clickerCost.toString());
    localStorage.setItem('multiplierCost', multiplierCost.toString());
    localStorage.setItem('clickerMultiplierCost', clickerMultiplierCost.toString());
    localStorage.setItem('amountGained', amountGained.toString());
    localStorage.setItem('clickerGain', clickerGain.toString());
    console.log('State saved:', { count, clickerCount, clickerCost, multiplierCost, clickerMultiplierCost, amountGained, clickerGain });
  }
  // This function initiates a clicker
  function startClicker() {
    const interval = setInterval(() => {
      count += clickerGain;
      saveState();
    }, 1000);
    clickerIntervals.push(interval);
  }

  function buyClicker() {
    if (count >= clickerCost) {
      count -= clickerCost;
      clickerCount += 1;
      clickerCost = Math.floor(clickerCost * 1.5);
      saveState();
      startClicker();
    } else {
      console.log("Not enough points to buy a clicker.");
    }
  }

  function buyMultiplier() {
    if (count >= multiplierCost) {
      count -= multiplierCost;
      amountGained *= 2;
      multiplierCost = Math.floor(multiplierCost * 2);
      saveState();
    } else {
      console.log("Not enough points to buy a multiplier.");
    }
  }

  function buyClickerMultiplier() {
    if (count >= clickerMultiplierCost) {
      count -= clickerMultiplierCost;
      clickerGain *= 2;
      clickerMultiplierCost = Math.floor(clickerMultiplierCost * 15);
      saveState();
    } else {
      console.log("Not enough points to buy a clicker multiplier.");
    }
  }

  function incrementCount() {
    count += amountGained;
    saveState();
  }

  function reset() {
    if (confirmReset) {
      count = 0;
      clickerCount = 0;
      clickerCost = 100;
      multiplierCost = 150;
      clickerMultiplierCost = 1000;
      clickerGain = 1;
      amountGained = 1;
      clickerIntervals.forEach(clearInterval);
      clickerIntervals = [];
      confirmReset = false;
      saveState();
    } else {
      confirmReset = true;
      setTimeout(() => { confirmReset = false; }, 3000); //reset confirmation after 3 seconds
    }
  }

  function quickReset() {
    count = 0;
    clickerCount = 0;
    clickerCost = 100;
    multiplierCost = 150;
    clickerMultiplierCost = 1000;
    clickerGain = 1;
    amountGained = 1;
    clickerIntervals.forEach(clearInterval);
    clickerIntervals = [];
    confirmReset = false;
    saveState();
  }

  function handleKeydown(e) {
    if (e.key === "Enter") {
      quickReset();
    } else {
      console.log("Key pressed:", e.key);
    }
  }

  // Clear and restart intervals on page load
  clickerIntervals.forEach(clearInterval);
  clickerIntervals = [];
  for (let i = 0; i < clickerCount; i++) {
    startClicker();
  }
</script>

<main>
  <Navbar />
  <h1>Welcome to the Great Realm of Bartholomue!</h1>
  <h2>The best site ever</h2>
  <button class="button" on:click={incrementCount} on:keydown={handleKeydown}>Pet Bartholomue ✋🐈</button>
  <p>Bartholomue has been petted {count} times.</p>
  <button class="resetbutton" on:click={reset}>
    {confirmReset ? "Are you sure?" : "Reset"}
  </button>
  <br><br>
  <button on:click={buyClicker} class="button">Add Clicker ({clickerCost} clicks)</button>
  <p>You have {clickerCount} clickers running, each adding {clickerGain} clicks per second!</p>
  <br>
  <button on:click={buyMultiplier} class="button">Add Multiplier ({multiplierCost} clicks)</button>
  <p>Each click is being multiplied by {amountGained} every click!</p>
  <br>
  <button on:click={buyClickerMultiplier} class="button">Add Clicker Multiplier ({clickerMultiplierCost} clicks)</button>
  <div class="photogallery">
    <h2>Photo Gallery</h2>
    <img src="bartholomue.png" alt="bartholomue the great">
    <img src="bartholomue.png" alt="bartholomue is life">
    <p>bartholomue</p>
  </div>
</main>

<style>
  .resetbutton {
    background-color: red;
    color: white;
    font-family: system-ui, Avenir, Helvetica, Arial, sans-serif;
    border: none;
    border-radius: 25px;
    padding: 10px 20px;
    cursor: pointer;
    transition: background-color 0.3s;
  }
  .photogallery {
    width: 200px;
    height: auto;
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
</style>