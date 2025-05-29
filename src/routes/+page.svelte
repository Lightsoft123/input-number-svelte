<script lang="ts">
	import InputNumber from "$lib/components/page/input/input-number.svelte";

	let tesValue: number = $state(0);
</script>

<InputNumber bind:realValue={tesValue} />
