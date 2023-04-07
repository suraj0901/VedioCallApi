<script>
	import {Peer} from "peerjs"
	const peer = new Peer()
	let codeid
</script>

<svelte:head>
	<title>Video Call Api</title>
	<meta name="description" content="Video Call Api" />
</svelte:head>

<h2>Hello</h2>
<div>
code : <input type="" bind:value={codeid} name="" />
</div>
