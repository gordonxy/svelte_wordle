<script lang="ts">
    export let correctSolution = ''
    export let isActive = false
    export let onProcessed: (isCorrect: boolean) => void

    enum StateClass {
        EMPTY = "empty",
        UNEVALUATED = "unevaluated",
        WRONG = "unevaluated",
        WRONG_POSITION = "wrong-position",
        CORRECT = "correct",
    }

    class Input {
        state = StateClass.EMPTY
        character = '*'
    }

    let currentInput = 0
    let inputs = [new Input(), new Input(), new Input(), new Input(), new Input()]

    function parseKey(event: KeyboardEvent) {
        if (!isActive) return

        let key = event.key

        if (key === "Enter" && currentInput == 5) {
            let uppercaseSolution = correctSolution.toUpperCase()
            let newStates = inputs.map((input, index) => {
                if(uppercaseSolution[index].toUpperCase() == input.character.toUpperCase()) {
                    return StateClass.CORRECT
                } else if (uppercaseSolution.includes(input.character.toUpperCase())) {
                    return StateClass.WRONG_POSITION
                }
                return StateClass.UNEVALUATED
            })
            
            onProcessed(newStates.every(state => { state == StateClass.CORRECT }))

            inputs = inputs.map((input, index) => {
                return {
                    state: newStates[index],
                    character: input.character,
                }
            })

            isActive = false
        } else if (key === "Backspace" && currentInput > 0) {
            currentInput -= 1
            inputs[currentInput].state = StateClass.EMPTY
            inputs[currentInput].character = '*'
        } else if (key.length == 1 && /^[A-Za-z]$/.test(key) && currentInput < 5) {
            inputs[currentInput].state = StateClass.UNEVALUATED
            inputs[currentInput].character = key
            
            currentInput += 1
        }
    }
</script>

<svelte:body on:keyup={event => parseKey(event)} />

<div class="row {isActive ? "active" : ""}">
    {#each inputs as {state, character}}
        <div class="character {state}">{character}</div>
    {/each}
</div>

<style>
    .row {
        margin: 2px;
    }

    .active {
        background-color: #FFFFFF11;
    }

    .character {
        display: inline-flex;

        vertical-align: middle;
        justify-content: center;
        font-family: 'Clear Sans' sans-serif;
        font-size: 4rem;
        font-weight: bold;
        color: rgb(255, 255, 255);
        text-transform: capitalize;

        border: 2px solid #5e5e5e;
        width: 4rem;
        line-height: 4rem;

        margin: 2px;
        padding: 5px;
    }

    .empty { 
        color: #FFFFFF00;
    }

    .unevaluated {
        background-color: #5e5e5e;
    }

    .wrong-position {
        background-color: #d1c235;
    }

    .correct {
        background-color: #1f9c1f;

    }
</style>