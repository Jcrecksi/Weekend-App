<!-- App.svelte -->

<script>
    let selectedActivities = [];
    let activity = '';
    let participants = 1; // Default number of participants
    let error = '';
    let weekendPlan = [];
    let showWeekendPlan = false;

    // Function to fetch activity suggestion
    async function fetchActivity() {
        const type = document.getElementById('activity-type').value;
        const apiUrl = `https://www.boredapi.com/api/activity?type=${type}&participants=${participants}`;

        try {
            const response = await fetch(apiUrl);
            if (!response.ok) {
                throw new Error('Failed to fetch data from Bored API');
            }
            const data = await response.json();
            // Check if activity is available
            if (typeof data.activity !== 'undefined') {
                activity = data.activity;
                error = '';
               
            } else {
                throw new Error('No activity found. Please choose another activity.');
            }
        } catch (error) {
            console.error('Error fetching activity:', error);
            error = error.message || 'Failed to fetch activity. Please try again.';
        }
    }

    // Function to handle accepting activity
    function acceptActivity() {
        if (selectedActivities.length < 8) {
            selectedActivities = [...selectedActivities, activity];
            updateSelectedActivitiesCount();
            fetchActivity(); // Fetch another activity after accepting the current one
        } else {
            error = 'You can select a maximum of 8 activities.';
        }
    }

    // Function to handle declining activity
    function declineActivity() {
        activity = '';
        error = '';
    }

    // Function to generate weekend plan
    function generatePlan() {
        weekendPlan = selectedActivities.map((activity, index) => `${index + 1}. ${activity}`);
        showWeekendPlan = true;
    }

    // Function to update selected activities count
    function updateSelectedActivitiesCount() {
        // Update selected activities count
    }

    // Function to handle restrictions on number of participants for educational type
    function handleParticipantRestriction() {
        const type = document.getElementById('activity-type').value;
        if (type === 'education') {
            participants = 1; // Restrict participants to 1 for educational type
        }
    }
</script>

<main class="min-h-screen bg-cover bg-center flex flex-col justify-center items-center" style="background-image: url(https://images.unsplash.com/photo-1588345921523-c2dcdb7f1dcd?q=80&w=870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D);">
    
    <h1 class="animate-bounce text-5xl font-bold mb-8">Weekend Planner</h1>
    <div class="w-full max-w-md p-6 bg-white rounded-lg shadow-lg">
        <form>
            <div class="mb-4">
                <label for="activity-type" class="block text-gray-700">Type of Activity:</label>
                <select id="activity-type"
                    class="w-full px-4 py-2 rounded-lg border border-gray-300 focus:border-blue-700 focus:outline-none" on:change={handleParticipantRestriction}>
                    <option value="education">Education</option>
                    <option value="recreational">Recreational</option>
                    <option value="social">Social</option>
                    <option value="diy">DIY</option>
                    <option value="charity">Charity</option>
                    <option value="cooking">Cooking</option>
                    <option value="relaxation">Relaxation</option>
                    <option value="music">Music</option>
                    <option value="busywork">Busywork</option>
                </select>
            </div>
            <div class="mb-4">
                <label for="participants" class="block text-gray-700">Number of Participants:</label>
                <input bind:value={participants} type="number" max="4" min="1"
                    class="w-full px-4 py-2 rounded-lg border border-gray-300 focus:border-blue-700 focus:outline-none">
            </div>
            <button type="button" on:click={fetchActivity}
                class="w-full bg-blue-700 text-white py-2 px-4 rounded-lg hover:bg-blue-700">Get Activity
                Suggestion</button>
        </form>
        {#if activity}
            <div class="mt-4 flex justify-between items-center">
                <button on:click={acceptActivity}
                    class="bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600">Accept</button>
                <button on:click={declineActivity}
                    class="bg-red-500 text-white py-2 px-4 rounded-lg hover:bg-red-600">Decline</button>
            </div>
            <div id="activity" class="mt-6">{activity}</div>
        {:else}
            <div class="text-red-500 mt-2">{error}</div>
        {/if}
        <h2 class="mt-6 text-lg font-semibold">Selected Activities:</h2>
        <div class="mt-2">{`You have selected ${selectedActivities.length} activities.`}</div>
        <div class="text-red-500 mt-2">{error}</div>
        <div id="weekend-plan" class="mt-4" class:hidden={!showWeekendPlan}>
            <h2 class="text-lg font-semibold mb-2">Weekend Plan:</h2>
            <ul>
                {#each weekendPlan as planItem}
                    <li>{planItem}</li>
                {/each}
            </ul>
        </div>
        <button on:click={generatePlan}
            class="w-full mt-4 bg-blue-700 text-white py-2 px-4 rounded-lg hover:bg-blue-700 disabled:opacity-50"
            disabled={selectedActivities.length === 0}>Generate Plan</button>
    </div>
</main>




