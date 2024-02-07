<script>
	export let group
	export let value
	export let checked
	
	$: updateChekbox(group)
	$: updateGroup(checked)
	
	function updateChekbox(group) {
		checked = group.indexOf(value) >= 0
	}
	
	function updateGroup(checked) {
		const index = group.indexOf(value)
		if (checked) {
			if (index < 0) {
				group.push(value)
				group = group
			}
		} else {
			if (index >= 0) {
				group.splice(index, 1)
				group = group
			}
		}
	}
</script>

<input
	type="checkbox"
	bind:checked={checked}
	
	value={value}
	/>