<script lang="ts">
    import WordleRow from './WordleRow.svelte';
    let solution = 'WORLD';
    let rowSolution = '';

    enum RowState {
        UNEVALUATED,
        IN_PROCESS,
        WRONG,
        CORRECT,
    }

    let rowStates = [RowState.IN_PROCESS, RowState.UNEVALUATED, RowState.UNEVALUATED, RowState.UNEVALUATED, RowState.UNEVALUATED, RowState.UNEVALUATED]

    function nextRoundOrEnd(isCorrect: boolean) {
        if (!isCorrect) {
            let nextRowDecided = false
            rowStates = rowStates.map(state => {
                if (state == RowState.IN_PROCESS) {
                    return isCorrect ? RowState.CORRECT : RowState.WRONG
                } else if (state == RowState.UNEVALUATED && !nextRowDecided) {
                    nextRowDecided = true
                    return RowState.IN_PROCESS
                }
                return state
            })
        }
    }
</script>
<div>
    <h1>WORDLE</h1>

    {#each rowStates as row}
    <WordleRow 
        correctSolution={solution}
        isActive={row == RowState.IN_PROCESS}
        onProcessed={(isCorrect) => nextRoundOrEnd(isCorrect)}
    />
    {/each}
</div>
<style>
    div {
		width: fit-content;
		margin: 0 auto;
    }

    h1 {
        text-align: center;
        font-size: 3em;
        color: #FFF;
        border-bottom: 1px solid #b9b9b9;
    }
</style>