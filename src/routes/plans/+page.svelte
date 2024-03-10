//JavaScript Section
<script>
    // Variables to store data
    let selectedActivities = []; // Array to store selected activities
    let activity = ''; // Current suggested activity
    let error = ''; // Current suggested activity
    let weekendPlan = []; // Array to store the finalized weekend plan
    let showWeekendPlan = false; // Boolean to control the visibility of weekend plan

    // Function to fetch activity suggestion from Bored API
    async function fetchActivity() {
        // Get the selected activity type from the dropdown
        const type = document.getElementById('activity-type').value;
        // Construct API URL based on the selected activity type
        const apiUrl = `https://www.boredapi.com/api/activity?type=${type}`;

        try {
            // Fetch data from Bored API
            const response = await fetch(apiUrl);
            // Check if response is successful
            if (!response.ok) {
                // Throw an error if response is not ok
                throw new Error('Failed to fetch data from Bored API');
            }
            // Parse response data as JSON
            const data = await response.json();
            // Update 'activity' variable with the fetched activity
            activity = data.activity;
            // Reset 'error' variable
            error = '';
        } catch (error) {
            // Log and handle errors during fetching activity
            console.error('Error fetching activity:', error);
            // Update 'error' variable with appropriate message
            error = error.message || 'Failed to fetch activity. Please try again.';
        }
    }

    // Function to handle accepting activity
    function acceptActivity() {
        // Check if maximum limit of 8 activities is reached
        if (selectedActivities.length < 8) {
            // Add current activity to selected activities
            selectedActivities = [...selectedActivities, activity];
             // Call function to update selected activities count (not implemented in this code)
            updateSelectedActivitiesCount();
            // Fetch another activity after accepting the current one
            fetchActivity(); // Fetch another activity after accepting the current one
        } else {
            // Set error message when maximum limit is reached
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
        // Map selected activities to format them as a list
        weekendPlan = selectedActivities.map((activity, index) => `${index + 1}. ${activity}`);
        // Set boolean to show weekend plan
        showWeekendPlan = true;
        // Clear screen to display only weekend plan
        clearScreen();
    }

    // Function to clear screen and show only the weekend plan
    function clearScreen() {
        // Hide activity form
        document.getElementById('activity-form').style.display = 'none';
        // Show weekend plan
        document.getElementById('weekend-plan').style.display = 'block';
    }

    // Function to update selected activities count (not implemented in this code)
    function updateSelectedActivitiesCount() {
        // Placeholder for updating selected activities count
    }
</script>

<!-- HTML Section -->
<main class="min-h-screen bg-cover bg-center flex flex-col justify-center items-center" style="background-image: url(https://images.unsplash.com/photo-1588345921523-c2dcdb7f1dcd?q=80&w=870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D);">
    
    <!-- Title -->
    <h1 class="animate-bounce text-5xl font-bold mb-8">Weekend Planner</h1>

     <!-- Activity Form -->
    <div class="w-full max-w-md p-6 bg-white rounded-lg shadow-lg" id="activity-form">
        <form>

            <!-- Button to fetch activity suggestion -->
            <div class="mb-4">
                <label for="activity-type" class="block text-gray-700">Type of Activity:</label>
                <select id="activity-type"
                    class="w-full px-4 py-2 rounded-lg border border-gray-300 focus:border-blue-700 focus:outline-none">
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
           
            <!-- Display section for fetched activity -->
            <button type="button" on:click={fetchActivity}
                class="w-full bg-blue-700 text-white py-2 px-4 rounded-lg hover:bg-blue-700">Get Activity
                Suggestion</button>
        </form>
        <!-- Display section for fetched activity -->
        {#if activity}
            <div class="mt-4 flex justify-between items-center">
                <!-- Buttons to accept or decline activity -->
                <button on:click={acceptActivity}
                    class="bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600">Accept</button>
                <button on:click={declineActivity}
                    class="bg-red-500 text-white py-2 px-4 rounded-lg hover:bg-red-600">Decline</button>
            </div>
            <!-- Display the fetched activity -->
            <div id="activity" class="mt-6">{activity}</div>
        {:else}
        <!-- Display error message if no activity fetched -->
            <div class="text-red-500 mt-2">{error}</div>
        {/if}
        <!-- Display selected activities count -->
        <h2 class="mt-6 text-lg font-semibold">Selected Activities:</h2>
        <div class="mt-2">{`You have selected ${selectedActivities.length} activities.`}</div>
        <!-- Display error message related to activity selection -->
        <div class="text-red-500 mt-2">{error}</div>
    </div>

    <!-- Weekend Plan Section -->
    <div id="weekend-plan" class="mt-4" style="display: none;">
        <!-- Title for weekend plan -->
        <h2 class="text-2xl font-bold mb-2">Weekend Plan:</h2>
        <div class="w-full max-w-md p-6 bg-white rounded-lg shadow-lg" id="activity-form">
        <!-- List to display selected activities -->
        <ul class="text-lg font-semibold mb-2">
            {#each weekendPlan as planItem}
                <li>{planItem}</li>
            {/each}
        </ul>
    </div>
    </div>
    <!-- Button to generate weekend plan -->
    <button on:click={generatePlan}
        class="w-auto mt-4 bg-blue-700 text-white py-2 px-4 rounded-lg hover:bg-blue-700 disabled:opacity-50"
        disabled={selectedActivities.length === 0}>Generate Plan</button>
</main>
