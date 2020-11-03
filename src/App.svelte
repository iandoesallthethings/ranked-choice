<script>
  import DragDropList from "./DragDropList.svelte";
  import { onMount } from "svelte";

  const sortByVotes = (a, b) => b.votes - a.votes;
  const filterCandidate = (list, toRemove) =>
    list.filter(candidate => candidate != toRemove);

  const updateBallots = () =>
    new Array(numberOfVoters).fill(candidates.slice());

  const countVotesForCandidate = (candidate, currentBallots) => {
    let votes = 0;
    currentBallots.forEach(ballot => {
      if (ballot[0] == candidate) votes++;
    });
    return { candidate, votes };
  };

  const calculateWinner = (currentCandidates, currentBallots) => {
    if (currentCandidates.length === 1) return currentCandidates[0];

    // Get results of this round
    const results = currentCandidates.map(candidate =>
      countVotesForCandidate(candidate, currentBallots)
    );

    // Remove lowest candidate
    const removed = results.sort(sortByVotes).pop().candidate;
    const newCandidates = filterCandidate(currentCandidates, removed);
    const newBallots = currentBallots.map(ballot =>
      filterCandidate(ballot, removed)
    );
    // Call Next Round
    return calculateWinner(newCandidates, newBallots);
  };

  let candidates = [
    "Donald J. Trump (R)",
    "Joseph R. Biden (D)",
    "Howie Hawkins (G)",
    "Jo Jorgensen (L)",
    "Kanye West (I)"
  ];

  let numberOfVoters = 5;
  let ballots = new Array(numberOfVoters).fill(candidates.slice());

  onMount(() => (ballots = updateBallots()));
</script>

<style>
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

  #ballots {
    display: flex;
    flex-flow: row wrap;
    align-items: center;
    justify-content: center;
    gap: 20px;
  }
  .ballot {
    flex: 1;
    min-width: 200px;
    max-width: 200px;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Ranked Choice Voting</h1>

  <label for="numberOfVoters">Number Of Voters</label>
  <input
    id="numberOfVoters"
    type="number"
    bind:value={numberOfVoters}
    on:change={() => (ballots = updateBallots())} />

  <h3>Winner: {calculateWinner(candidates, ballots)}</h3>

  <h2>Ballots</h2>
  <div id="ballots">
    {#each ballots as ballot, i}
      <div class="ballot">
        <h3>Voter {i}</h3>
        <DragDropList bind:data={ballot} />
      </div>
    {/each}
  </div>
</main>
