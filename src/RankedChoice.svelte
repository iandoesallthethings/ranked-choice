<script>
  import DragDropList from "./DragDropList.svelte";
  import { fly } from "svelte/transition";
  export let candidates;
  export let ballots;

  const sortByVotesDescending = (a, b) => b.votes - a.votes;
  const filterCandidate = (list, toRemove) =>
    list.filter(candidate => candidate != toRemove);

  const countVotesForCandidate = (candidate, currentBallots) => {
    let votes = 0;
    currentBallots.forEach(ballot => {
      if (ballot[0] == candidate) votes++;
    });
    return { candidate, votes };
  };

  // TODO: Implement Ties
  const calculateWinner = (currentCandidates, currentBallots) => {
    if (currentCandidates.length === 1) return currentCandidates[0];

    // Get results of this round
    const results = currentCandidates.map(candidate =>
      countVotesForCandidate(candidate, currentBallots)
    );

    // Remove lowest candidate
    const removed = results.sort(sortByVotesDescending).pop().candidate;
    const newCandidates = filterCandidate(currentCandidates, removed);
    const newBallots = currentBallots.map(ballot =>
      filterCandidate(ballot, removed)
    );
    // Call Next Round
    return calculateWinner(newCandidates, newBallots);
  };
</script>

<h3>Winner: {calculateWinner(candidates, ballots)}</h3>

<div id="ballots">
  {#each ballots as ballot, i}
    <div class="ballot" transition:fly>
      <h3>Voter {i}</h3>
      <DragDropList bind:data={ballot} />
    </div>
  {/each}
</div>

<style>
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
</style>
