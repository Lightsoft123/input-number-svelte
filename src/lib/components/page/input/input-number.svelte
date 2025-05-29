<script lang="ts">
	import Input from "$lib/components/ui/input/input.svelte";
	import type { WithElementRef } from "$lib/utils";
	import type { HTMLInputAttributes, HTMLInputTypeAttribute } from "svelte/elements";

	type InputType = Exclude<HTMLInputTypeAttribute, "file">;

	type Props = WithElementRef<
		Omit<HTMLInputAttributes, "type"> &
			({ type: "file"; files?: FileList } | { type?: InputType; files?: undefined })
	>;

	let { realValue = $bindable(), value, ...restProps }: { realValue?: number } & Props = $props();
</script>

<Input
	bind:value={
		() => realValue,
		(val) => {
			if (Number(val)) realValue = val;
		}
	}
	type="hidden"
	{...restProps}
/>
<Input
	value={realValue?.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")}
	{...restProps}
	onkeydown={(e) => {
		if (e.key == "." || ("-".includes(value) && e.key == "-")) e.preventDefault();

		const start = e.currentTarget.selectionStart || 0;
		const end = e.currentTarget.selectionEnd || 0;

		const startStep = e.currentTarget.value.substring(0, start);
		const endStep = e.currentTarget.value.substring(end, e.currentTarget.value.length);

		if (/^\d+$/.test(e.key) || e.key === "Delete" || e.key === "Backspace") {
			e.currentTarget.value = e.currentTarget.value.replaceAll(".", "");
			console.log(
				Number(
					e.currentTarget.value +
						realValue
							?.toString()
							.substring(
								start - Array.from(startStep as string).filter((val: string) => val === ".").length,
								end + Array.from(endStep as string).filter((val: string) => val === ".").length
							)
				)
			);

			realValue = Number(
				e.currentTarget.value +
					realValue
						?.toString()
						.substring(
							start - Array.from(startStep as string).filter((val: string) => val === ".").length,
							end + Array.from(endStep as string).filter((val: string) => val === ".").length
						)
			);
		}
	}}
/>
