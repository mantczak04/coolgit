<script lang="ts">
    let text: string = "";
    let lastInRow: number = 0;

    const CURRENT_YEAR: number = 2024;

    let letterPositions: number[] = [];

    let commitDates : string[] = [];
 

    interface CharacterToNumbersMap{
        [letter:string] : number[];
    }

    const characterToNumbers : CharacterToNumbersMap = {
        a: [2, 3, 4, 5, 6, 7, 8, 11, 18, 15, 23, 24, 25, 26, 27, 28],
        b: [1, 2, 3, 4, 5, 6, 7, 8, 11, 18, 25, 14, 15, 21, 23, 24, 26, 27],
        c: [1,7,2,3,4,5,6,14,21,28,8,15,22],
        d: [1,2,3,4,5,6,7,8,14,15,21,23,24,25,26,27],
        e: [1,2,3,4,5,6,7,8,15,22, 11,18,25,14,21,28],
        f: [1,2,3,4,5,6,7,8,15,22,11,18,25],
        g: [1,2,3,4,5,6,7,8,15,22,7,14,21,28,27,26,25,18],
        h: [1,2,3,4,5,6,7,22,23,24,25,26,27,28,11,18],
        i: [1,3,4,5,6,7], // LENGTH: 1
        j: [22,23,24,25,26,27,28,21,14,5,6,1,8,15],
        k: [1,2,3,4,5,6,7,10,11,16,19,22,27,28],
        l: [1,2,3,4,5,6,7,14,21,28],
        m: [1,2,3,4,5,6,7,9,17,23,29,30,31,32,33,34,35], // LENGTH: 5
        n: [1,2,3,4,5,6,7,10,18,22,23,24,25,26,27,28],
        o: [1,7,22,28,2,3,4,5,6,21,14,27,26,25,24,23  ,15,8],
        p: [1,2,3,4,5,6,7,8,15,23,24,11,18],
        r: [1,2,3,4,5,6,7,8,15,23,24,11,18, 26,27,28],
        s: [2,3,8,15,22,11,18,26,27,7,14,21],
        t: [1,8,15,16,17,18,19,20,21,22,29], // LENGTH: 5
        u: [1,2,3,4,5,6,14,21,27,26,25,24,23,22],
        q: [22,28,2,3,4,5,13,20,27,33,32,31,30,8,15,22,35], // LENGTH: 5
        v: [1,2,3,4,5,13,21,27,29,30,31,32,33], // LENGTH: 5
        w: [1,2,3,4,5,6,7,14,20,28,29,30,31,32,33,34,35], //LENGTH : 5 
        x: [1,2,10,12,6,7,18,24,26,29,30,34,35], //LENGTH: 5
        y: [1,2,3,4,11,18,22,23,24,25,26,20,7,14],
        z: [1,8,15,22,29,30,24,18,12,6,7,14,21,28,35], // LENGTH:5

        1: [2,8,9,10,11,12,13,14], //LENGTH: 2
        2: [2,8,15,23,24,18,12,6,7,14,21,28],
        3: [2, 6, 8,15, 23,24,11,18,26,27,14,21],
        4: [4,5,6,20,27,34,28,27,26,25,24,23,22,16,10,13], //LENGTH: 5
        5: [],
        6: [1,8,15,22,2,3,4,5,6,7,14,21,28,25,26,27,11,18],
        7: [1,8,15,22,23,17,18,19,20,21],
        8: [1,8,15,22,4,7,14,21,28,25,26,27,11,18,23,24,2,3,5,6],
        9: [1,8,15,22,4,7,14,21,28,25,26,27,11,18,23,24,2,3],
        0: [8,15,2,3,4,5,6,23,24,25,26,27,14,21],
    }

    function moveCharacterByValue(array: number[], value: number): number[]{
        return array.map((number) => number+value);
    }

    function daysIntoDates(days: number[]): string[] {
    let dates: string[] = [];
    days = days.sort((a,b) => a-b);
    for(const day of days){
        let newDate: Date = new Date(2024, 0); // Start at Jan 1, 2024
        newDate.setDate(day); // Set the day of the year
        let newStringDate = newDate.toLocaleDateString('en-GB', {
            day: '2-digit',
            month: '2-digit',
            year: 'numeric'
        });
        newStringDate = newStringDate.replace(/\//g, '-');
        dates.push(newStringDate);
    }
    return dates;
}

    function handleEnterKey(event: KeyboardEvent): void{
        if(event.key === 'Enter'){
            generateText();
        }
    }
    
    function generateText(): void {
        text = text.toLocaleLowerCase();
        letterPositions.splice(0, letterPositions.length);
        let letters:number = 0;
        let differenceValue=35;

        const lengthFiveLetters = ["m","t","q","w","v","x","z"];
        for(let i: number=0; i<text.length; i++){
            if(text.charAt(i) === 'i') differenceValue=14;
            else if(text.charAt(i) === '1') differenceValue=21;
            else if(text.charAt(i) === ' ') {
                letters+=7;
                continue;
            }
            else if(lengthFiveLetters.includes(text.charAt(i))){
                differenceValue=42;
            }
            else differenceValue=35;
            let currentLetterArray = characterToNumbers[text.charAt(i)];
            letterPositions = letterPositions.concat(moveCharacterByValue(currentLetterArray, letters));

            letters+=differenceValue;
        }
        commitDates = (daysIntoDates(letterPositions));
    }

</script>

<div class="main-container">
    <div class="app">   
        <h1>coolgit.io {CURRENT_YEAR}</h1>
        <h2>generate text to Your contributions section at github :)</h2>
        <input
            bind:value={text}
            on:keydown={handleEnterKey}
            class="input"
            type="text"
            placeholder="your text goes here"
        />
        <button on:click={generateText}>generate!</button>
    </div>

    <div class="contributions">
        <div class="graph">
            <ul class="months">
                <li>Jan</li>
                <li>Feb</li>
                <li>Mar</li>
                <li>Apr</li>
                <li>May</li>
                <li>Jun</li>
                <li>Jul</li>
                <li>Aug</li>
                <li>Sep</li>
                <li>Oct</li>
                <li>Nov</li>
                <li>Dec</li>
            </ul>
            <ul class="days">
                <li>Sun</li>
                <li>Mon</li>
                <li>Tue</li>
                <li>Wed</li>
                <li>Thu</li>
                <li>Fri</li>
                <li>Sat</li>
            </ul>
            <ul class="squares">
                {#each { length: 366 } as _, i}
                    {#if letterPositions.includes(i+1) }
                        <li data-level="1"></li>
                    {:else}
                        <li data-level="0"></li>
                    {/if}
                {/each}
            </ul>
        </div>
        <div class='bottom-container'>
            <h3>you need to commit at :</h3>
    <div class="commits-container">
        <ul class="commits">
            {#each commitDates as day}
                <li>{day}</li>
            {/each}
        </ul>
    </div>
    </div>
    </div>
</div>

<style>
    /*I'VE NOT CREATED THIS GRID - IT ALL BELONGS TO bitsofco.de!!! */

    /* Article - https://bitsofco.de/github-contribution-graph-css-grid/ */
    /* Grid-related CSS */
    .commits{
        font-size: 18px;
    }

    .bottom-container{
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .commits-container{
        background-color: #161B22;
        width: 40%;
        overflow-y: auto;
        max-height: 184px; /* Ustaw maksymalną wysokość kontenera */
        border: 1px white solid;
        border-radius: 4px;
    }
    
    .contributions {
        margin-top: 2px;
    }
    ul {
        list-style-type: none;
    }
    :root {
        --square-size: 15px;
        --square-gap: 5px;
        --week-width: calc(var(--square-size) + var(--square-gap));
    }

    .months {
        grid-area: months;
    }
    .days {
        grid-area: days;
    }
    .squares {
        grid-area: squares;
        color: white;
    }

    .graph {
        display: inline-grid;
        grid-template-areas:
            "empty months"
            "days squares";
        grid-template-columns: auto 1fr;
        transform: translateX(-28px);
        margin-top: 12px;
    }

    .months {
        display: grid;
        grid-template-columns:
            calc(var(--week-width) * 4) /* Jan */
            calc(var(--week-width) * 4) /* Feb */
            calc(var(--week-width) * 4) /* Mar */
            calc(var(--week-width) * 5) /* Apr */
            calc(var(--week-width) * 4) /* May */
            calc(var(--week-width) * 4) /* Jun */
            calc(var(--week-width) * 5) /* Jul */
            calc(var(--week-width) * 4) /* Aug */
            calc(var(--week-width) * 4) /* Sep */
            calc(var(--week-width) * 5) /* Oct */
            calc(var(--week-width) * 4) /* Nov */
            calc(var(--week-width) * 5) /* Dec */;
    }

    .days,
    .squares {
        display: grid;
        grid-gap: var(--square-gap);
        grid-template-rows: repeat(7, var(--square-size));
    }

    .squares {
        grid-auto-flow: column;
        grid-auto-columns: var(--square-size);
    }

    .days li:nth-child(odd) {
        visibility: hidden;
    }

    .squares li {
        background-color: #161B22;
        border-radius: 2px;
    }

    .squares li[data-level="1"] {
        background-color: #006D32;
    }

    .squares li[data-level="2"] {
        background-color: #39D353;
    }

    .squares li[data-level="3"] {
        background-color: #196127;
    }

    button {
        background-color: #7de6a5;
        width: auto;
        height: 40px;
        border-radius: 4px;
        font-family: "Space Mono", monospace;
        font-weight: 400;
        font-style: normal;
    }

    button:hover {
        cursor: pointer;
    }
    .input {
        margin-top: 32px;
        width: 200px;
        height: 34px;
        border-radius: 4px;
        font-family: "Space Mono", monospace;
        font-weight: 400;
        font-style: normal;
    }
    .main-container {
        margin-top: 24px;
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

    :global(body) {
        text-align: center;
        color: white;
        background-color: #0D1117;
        font-family: "Space Mono", monospace;
        font-weight: 400;
        font-style: normal;
    }
</style>
