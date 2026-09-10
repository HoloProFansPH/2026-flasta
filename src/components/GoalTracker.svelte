<script>
    import GoalItem from "./GoalItem.svelte";
    let currentGoalPercentage = $state(0);
    let currentGoalName = $state("");
    let currentCheckpointPHP = $state(0);
    let donationAmount = $state(0);
    let deadline = $state(Date.now());
    let time = $state(Date.now());
    let timerString = $state("0d 0h 0m 0s");
    async function fetchData() {
        const fetchDataResponse = await fetch(
            "https://flasta2026-api.holoprofansph.org/api/goalstatus",
            {
                method: "GET",
                headers: {
                    auth: "oh hi",
                },
            },
        );
        const dataResponse = await fetchDataResponse.json();
        //console.log(dataResponse);
        currentGoalPercentage = (
            dataResponse.currentGoalPercentage * 100
        ).toFixed(2);
        currentGoalName = dataResponse.currentGoalName;
        currentCheckpointPHP = dataResponse.currentCheckpointPHP.toFixed(2);
        donationAmount = dataResponse.donationAmount.toFixed(2);
        deadline = new Date(dataResponse.deadline);
        //console.log(deadline);
        //console.log(time);
        time = deadline - Date.now();
        //console.log(time);
        return dataResponse;
    }
    let promise = $state(fetchData());
    // let days = $state(0);
    function Timer() {
        time = deadline - Date.now();
        //console.log(time);
        let days = Math.floor(time / (1000 * 60 * 60 * 24));
        let hours = Math.floor((time / (1000 * 60 * 60)) % 24);
        let minutes = Math.floor((time / 1000 / 60) % 60);
        let seconds = Math.floor((time / 1000) % 60);
        return days + "d " + hours + "h " + minutes + "m " + seconds + "s";
    }
    $effect(() => {
        const interval = setInterval(() => {
            timerString = Timer();
        }, 1000);
        return () => {
            clearInterval(interval);
        };
    });
    function numberWithCommas(x) {
        return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    }
</script>

<div class="card">
    <div class="cardHeader">
        <span class="cardTitle">Goal</span>
        <span id="goalNameDesktop">In Progress: <i>{currentGoalName}</i></span>
        <!-- <span id="goalNameDesktop">Completed: <i>{currentGoalName}</i></span> -->
        <span class="cardTitle">{currentGoalPercentage}%</span>
        <!-- <span id="goalNameDesktop">Completed: <i>Stretch Goal</i></span>
        <span class="cardTitle">0.00%</span> -->
    </div>
    <div id="goalMeter">
        <!-- <div id="goalMeterComplete" style={{width: (currentGoalPercentage * 100) + '%'}}></div> -->
        <div
            id="goalMeterComplete"
            style="width: {currentGoalPercentage}%"
        ></div>
    </div>
    <!-- <span id="goalNameMobile">In Progress: <i>{currentGoalName}</i></span> -->
    <span id="goalNameMobile">Completed: <i>Stretch Goal</i></span>
    <div class="goalMeterData">
        <span><i class="fa fa-clock"></i>{timerString}</span>
        <span
            ><i class="fa fa-flag-checkered"></i> PHP {numberWithCommas(
                donationAmount,
            )} / PHP {numberWithCommas(currentCheckpointPHP)}</span
        >
        <!-- <span
            ><i class="fa fa-flag-checkered"></i> PHP 39,117.77 / PHP 36,200.00</span
        > -->
    </div>
    <div class="tableFlex">
        <GoalItem
            goalName="Base Goal"
            goalDescription="Three flower stands with 11 chibi standees attached"
            goalAmount="PHP 30,500 ~USD 534"
        />
        <!-- <GoalItem
            complete
            goalName="Stretch Goal"
            goalDescription="Four flower stands with chibi standees attached to flower stands and full-body standees"
            goalAmount="PHP 36,200 ~USD 620"
        /> -->
        <!-- <span class="tableLeft">
                    <b>Notes:</b><br/>The Minimum Goal is due on April 14, 2025 11:59 AM PHT for the the project to comfortably proceed on April 26, 2025. Fans can still donate until April 30, 2025 11:59 AM PHT. Airdate is subject to change depending on airslot availability.
                    </span> -->
    </div>
    {#await promise}
        <!-- <span>Loading data...</span> -->
    {:then data}
        <!-- <span> Data Loaded </span> -->
    {:catch error}
        <h2>-ERROR</h2>
        <span>{error.message}</span>
    {/await}
    <p>
        For international transactions, the exchange rate is calculated as USD 1
        = PHP 57.20, which is derived from the conversion rate provided by
        PayPal (Kofi's payment processor) on September 6, 2026, in addition to
        other conversion and international transfer fees issued by PayPal. The
        actual, current exchange rate may differ from the rate used for this
        tracker.
    </p>
</div>
