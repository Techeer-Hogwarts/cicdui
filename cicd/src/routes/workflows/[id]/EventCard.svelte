<script context="module" lang="ts">
	export type Event = {
		imageName: string;
		imageVersion: string;
		timestamp: string;
		numberOfReplicas?: number; // Only for CD type workflows
	};
</script>

<script lang="ts">
	export let event: Event;
	export let type: "CI" | "CD";
	export let onDeploy: (() => void) | undefined;
</script>

<div class="bg-card text-card-foreground rounded-lg border p-4 flex flex-col md:flex-row items-center justify-between gap-4">
	<div class="grid grid-cols-1 md:grid-cols-4 gap-4 flex-1">
		<div>
			<p class="text-muted-foreground text-sm font-medium">Image Name</p>
			<p class="font-mono text-sm">{event.imageName}</p>
		</div>
		<div>
			<p class="text-muted-foreground text-sm font-medium">Image Version</p>
			<p class="font-mono text-sm">{event.imageVersion}</p>
		</div>
		<div>
			<p class="text-muted-foreground text-sm font-medium">Timestamp</p>
			<p class="font-mono text-sm">{event.timestamp}</p>
		</div>

		{#if type === "CD" && event.numberOfReplicas !== undefined}
			<div>
				<p class="text-muted-foreground text-sm font-medium">Replicas</p>
				<p class="font-mono text-sm">{event.numberOfReplicas}</p>
			</div>
		{/if}
	</div>

	{#if type === "CD" && onDeploy}
		<button class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600"
		on:click={onDeploy}>
			Trigger Deployment
		</button>
	{/if}
</div>

