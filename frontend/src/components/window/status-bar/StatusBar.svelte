<script lang="ts">
	import { getContext, onMount } from "svelte";

	import { platformIsMac } from "@/utility-functions/platform";
	import { type HintData, type HintInfo, type LayoutKeysGroup, UpdateInputHints } from "@/wasm-communication/messages";

	import LayoutRow from "@/components/layout/LayoutRow.svelte";
	import Separator from "@/components/widgets/labels/Separator.svelte";
	import UserInputLabel from "@/components/widgets/labels/UserInputLabel.svelte";
	import type { Editor } from "@/wasm-communication/editor";

	const editor = getContext<Editor>("editor");

	let hintData: HintData = [];

	function inputKeysForPlatform(hint: HintInfo): LayoutKeysGroup[] {
		if (platformIsMac() && hint.keyGroupsMac) return hint.keyGroupsMac;
		return hint.keyGroups;
	}

	onMount(() => {
		editor.subscriptions.subscribeJsMessage(UpdateInputHints, (data) => {
			hintData = data.hintData;
		});
	});
</script>

<LayoutRow class="status-bar">
	<LayoutRow class="hint-groups">
		{#each hintData as hintGroup, index (hintGroup)}
			{#if index !== 0}
				<Separator type={"Section"} />
			{/if}
			{#each hintGroup as hint (hint)}
				{#if hint.plus}
					<LayoutRow class="plus">+</LayoutRow>
				{/if}
				<UserInputLabel mouseMotion={hint.mouse} keysWithLabelsGroups={inputKeysForPlatform(hint)}>{hint.label}</UserInputLabel>
			{/each}
		{/each}
	</LayoutRow>
</LayoutRow>

<style lang="scss" global>
	.status-bar {
		height: 24px;
		width: 100%;
		flex: 0 0 auto;

		.hint-groups {
			flex: 0 0 auto;
			max-width: 100%;
			margin: 0 -4px;
			overflow: hidden;

			.separator.section {
				margin: 0;
			}

			.plus {
				flex: 0 0 auto;
				align-items: center;
				font-weight: 700;
			}

			.user-input-label {
				margin: 0 8px;

				& + .user-input-label {
					margin-left: 0;
				}
			}
		}
	}
</style>