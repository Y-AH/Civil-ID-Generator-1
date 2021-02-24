<script lang="ts">
  let selectedDay: string = "01";
  let selectedMonth: string = "01";
  let selectedYear: string = new Date().getFullYear().toString();

  let error = "";

  let generated: string;

  const days = [];
  const months = [];
  const years = [];

  for (let i = 1; i <= 31; i += 1) {
    days.push(i.toString().padStart(2, "0"));
  }

  for (let i = 1; i <= 12; i += 1) {
    months.push(i.toString().padStart(2, "0"));
  }

  for (let i = new Date().getFullYear(); i > 1900; i -= 1) {
    years.push(i.toString());
  }

  function checkCivilId(civilId: string): boolean {
    if (!/^\d{12}$/.test(civilId)) {
      return false;
    }
    const coeff = [2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2];
    const idDigits = civilId.split("").map((d) => parseInt(d, 10));
    const summation = coeff.reduce((p, c, i) => p + c * idDigits[i], 0);
    const lastDigit = 11 - (summation % 11);
    return lastDigit === idDigits[11];
  }

  function generate(): void {
    error = "";
    if (["04", "06", "09", "11"].includes(selectedMonth)) {
      if (selectedDay === "31") {
        error = `Month ${selectedMonth} has only 30 days.`;
      }
    }
    if (selectedMonth === "02") {
      if (selectedDay === "30" || selectedDay === "31") {
        error = "Feb has only 28 days in non-leap years.";
      } else if (selectedDay === "29") {
        const y = parseInt(selectedYear);
        if (!(y % 4 === 0 && y % 100 !== 0)) {
          error = "Feb has only 28 days in non-leap years.";
        }
      }
    }
    if (error.trim() === "") {
      let start = "";
      const year = parseInt(selectedYear);
      const month = parseInt(selectedMonth);
      const day = parseInt(selectedDay);

      if (year < 2000) {
        start += "2";
      } else {
        start += "3";
      }

      start += selectedYear.substring(2) + selectedMonth + selectedDay;
      let random = Math.floor(Math.random() * 100000);
      let civilId = start + random.toString();
      while (!checkCivilId(civilId)) {
        random = (random + 1) % 100000;
        civilId = start + random.toString();
      }
      generated = civilId;
    }
  }
</script>

<main>
  <h1>Fake Civil ID Generator</h1>
  <article>
    <h2>Date Of Birth</h2>
    <div class="row">
      <div class="column">
        <h3 label-for="select-day">Choose Date</h3>
        <select id="select-day" bind:value={selectedDay}>
          {#each days as day}
            <option value={day}>{parseInt(day)}</option>
          {/each}
        </select>
      </div>
      <div class="column">
        <h3>Choose Month</h3>
        <select bind:value={selectedMonth}>
          {#each months as month}
            <option value={month}>{parseInt(month)}</option>
          {/each}
        </select>
      </div>
      <div class="column">
        <h3>Select Year</h3>
        <select bind:value={selectedYear}>
          {#each years as year}
            <option value={year}>{parseInt(year)}</option>
          {/each}
        </select>
      </div>
    </div>
    {#if error.trim() !== ""}
      <h2 class="error">Error</h2>
      <p class="error">{error}</p>
    {/if}
    <button on:click={generate} class="button">Generate Civil ID</button>
    {#if generated !== undefined && generated !== null && generated.trim() !== ""}
      <div>
        <h3>Generated Civil ID is {generated}</h3>
      </div>
    {/if}
  </article>
</main>

<style lang="scss">
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  .row {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: auto;
  }

  .column {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 16px;
  }
  .button {
    border: none;
    background: #ff3e00;
    color: white;
    padding: 8px 16px;
    box-shadow: 16px 16px 16px;
    cursor: pointer;
  }

  .button:hover {
    background: #bd3001;
  }

  .button:active {
    background: #bd3001;
  }

  .button:focus {
    background: #bd3001;
  }

  .error {
    color: #b11302;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
