<script lang="ts">
	import { onMount } from "svelte";
	import EventCard, { type Event } from "./EventCard.svelte";
	import { page } from "$app/stores";

	let workflowId: string;
	let workflowType: "CI" | "CD" = "CI";
	let events: Event[] = [];

	onMount(() => {
		const params = $page.params;
		workflowId = params.id;
        // Example logic to determine workflow type
        // In a real application, you would fetch this from an API
        // For example: workflowType = fetchWorkflowType(workflowId);
        console.log(`Workflow ID: ${workflowId}`);
        if (workflowId === "3") {
            workflowType = "CD";
        } else {
            workflowType = "CI";
        }
		// Fetch workflow details (mock data here)
		// workflowType = workflowId.includes("cd") ? "CD" : "CI"; // Example logic
        console.log(`Workflow Type: ${workflowType}`);
		// Fetch events (replace with API call)
		events = [
			{ imageName: "nginx", imageVersion: "1.23", timestamp: "2024-03-18T12:00:00Z" },
			{ imageName: "redis", imageVersion: "7.0", timestamp: "2024-03-18T13:30:00Z", numberOfReplicas: 3 },
		];
	});

	function triggerDeployment(event: Event) {
		console.log(`Triggering deployment for ${event.imageName}:${event.imageVersion}`);
	}
</script>

<div class="container mx-auto p-6">
	<h1 class="text-2xl font-semibold mb-4">Workflow Events - {workflowId}</h1>

	<div class="grid gap-4">
		{#each events as event}
			<EventCard event={event} type={workflowType} onDeploy={workflowType === "CD" ? () => triggerDeployment(event) : undefined} />
		{/each}
	</div>
</div>
