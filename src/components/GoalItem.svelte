<script lang="ts">
    interface Props {
        goalName: string;
        goalDescription: string;
        goalAmount: string;
        complete?: boolean;
        ongoing?: boolean;
    }
    let { goalName, goalDescription, goalAmount, complete, ongoing }: Props =
        $props();
</script>

<div
    class={`goalItem ${complete ? "complete" : ""}${ongoing ? "ongoing" : ""}`}
>
    <div class="goalItemLeft">
        <span>
            {#if complete}
                <i class="fas fa-check-circle"></i>
            {:else}
                <i class="fas fa-trophy"></i>
            {/if}
            <b>{goalName}:</b>
        </span>
        <span>{goalDescription}</span>
    </div>
    <div class="goalItemRight">
        {goalAmount}
    </div>
    <!-- <span class="tableLeft"><i class="fas fa-trophy"></i><b>{goalName}</b>: {goalDescription}</span>
    <span class="tableRight">{goalAmount}</span> -->
</div>
