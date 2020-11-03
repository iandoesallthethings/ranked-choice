<script>
  import { onMount } from "svelte";
  import RankedChoice from "./RankedChoice.svelte";
  import Approval from "./Approval.svelte";
  import Plurality from "./Plurality.svelte";

  let electionTypes = [
    { name: "Plurality", component: Plurality },
    { name: "Ranked Choice", component: RankedChoice },
    { name: "Approval", component: Approval }
  ];

  let currentElectionType = electionTypes[0];

  const updateBallots = () =>
    new Array(numberOfVoters).fill(candidates.slice());

  const updateCandidates = () => candidateText.split("\n");

  const updateElection = () => {
    candidates = updateCandidates();
    ballots = updateBallots();
  };

  let candidates = [
    "Donald J. Trump (R)",
    "Joseph R. Biden (D)",
    "Bernard Sanders (S)",
    "Howie Hawkins (G)",
    "Jo Jorgensen (L)",
    "Kanye West (I)"
  ];
  let candidateText = candidates.join("\n");

  let numberOfVoters = 5;
  let ballots = updateBallots();

  onMount(() => (ballots = updateBallots()));
</script>

<nav id="options">
  <div class="option">
    <h4>Election Type</h4>
    {#each electionTypes as electionType}
      <input
        id="{electionType.name}tab"
        type="radio"
        bind:group={currentElectionType}
        value={electionType}
        on:change={updateElection} />
      <label class="tab" for="{electionType.name}tab">
        {electionType.name}
      </label>
    {/each}
  </div>

  <div class="option">
    <h4>Number of Voters</h4>
    <input
      id="numberOfVoters"
      type="number"
      bind:value={numberOfVoters}
      on:change={updateElection} />
  </div>
  <div class="option">
    <h4>Candidates</h4>
    <textarea
      type="text"
      rows={candidates.length}
      bind:value={candidateText}
      on:change={updateElection} />
  </div>
</nav>

<main>
  <h1>{currentElectionType.name}</h1>

  <svelte:component
    this={currentElectionType.component}
    {candidates}
    {ballots} />
</main>

<style>
  main {
    text-align: center;
    padding: 0 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    margin: 0.5em 0;
  }
  h4 {
    color: #ff3e00;
    font-weight: 200;
    margin: 0.5em 0;
  }

  #options {
    /* max-width: fit-content; */
    display: flex;
    gap: 20px;
  }
  /* .option {
    margin-bottom: 1em;
  } */

  /* Election Type Options */
  input[type="radio"] {
    display: none;
  }
  input[type="radio"] + label {
    cursor: pointer;
  }
  input[type="radio"]:checked + label {
    /* border-bottom: 1px solid red; */
    /* text-decoration: underline; */
    font-weight: 500;
  }

  /* Ballot Options */
  input[type="number"] {
    width: 70px;
  }
  /* Candidate Options */
  textarea {
    overflow: auto;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
