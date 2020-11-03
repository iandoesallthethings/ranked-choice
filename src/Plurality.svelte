<script>
  import { fly } from "svelte/transition";
  export let candidates;
  export let ballots;

  let completedBallots = new Array(ballots.length);

  const sortByVotesDescending = (a, b) => b.votes - a.votes;

  const countVotesForCandidate = candidate => {
    let votes = 0;
    completedBallots.forEach(ballotVote => {
      if (ballotVote === candidate) votes++;
    });
    return { candidate, votes };
  };

  const calculateWinner = (candidates, completedBallots) => {
    const results = candidates.map(countVotesForCandidate);
    results.sort(sortByVotesDescending);

    if (results[0].votes === 0) return "No Ballots Cast Yet";
    else if (results[0].votes === results[1].votes)
      return `Tie: ${results[0].candidate} & ${results[1].candidate}`;
    else return `Winner: ${results[0].candidate}`;
  };
</script>

<h3>{calculateWinner(candidates, completedBallots)}</h3>

<div id="ballots">
  {#each ballots as ballot, i}
    <div class="ballot" transition:fly>
      <h3>Voter {i}</h3>
      {#each ballot as candidate}
        <label for="{i}{candidate}">
          <div class="ballotItem">
            <input
              id="{i}{candidate}"
              type="radio"
              value={candidate}
              bind:group={completedBallots[i]} />
            <span>{candidate}</span>
          </div>
        </label>
      {/each}
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
  .ballotItem {
    box-sizing: border-box;
    display: inline-flex;
    width: 100%;
    min-height: 3em;
    margin-bottom: 0.5em;
    background-color: white;
    border: 1px solid rgb(190, 190, 190);
    border-radius: 2px;
    padding: 32px 0;
    user-select: none;
  }
  .ballotItem > * {
    margin: auto;
  }
  input[type="radio"] {
    display: inline;
  }
</style>
