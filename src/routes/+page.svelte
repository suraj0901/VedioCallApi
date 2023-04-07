<script>
	import { Peer } from 'peerjs';
	const peer = new Peer();
	let codeid = '';
	/**
	 * @type {string}
	 */
	let yourId;

	/**
	 * @type {HTMLVideoElement}
	 */
	let vidoCurrent;
	/**
	 * @type {HTMLVideoElement}
	 */
	let videoElm;
	const renderYourWebCam = (/** @type {MediaProvider | null} */ stream) => {
		console.log({ stream });
		videoElm.srcObject = stream;
		videoElm.play();
	};

	peer.on('open', (id) => {
		console.log(`your id ${id}`);
		yourId = id;
	});

	peer.on('error', (id) => {
		console.log(`error id ${id}`);
	});

	// peer.on('connection', (conn) => {
	// 	console.log('...message');
	// 	conn.on('data', (data) => {
	// 		console.log('new Data ' + data);
	// 	});
	// 	conn.on('open', () => {
	// 		console.log('new Message');
	// 	});
	// });

	peer.on('call', async (call) => {
		await navigator.mediaDevices
			.getUserMedia({
				video: true,
				audio: true
			})
			.then((stream) => { 
				call.answer(stream);
				call.on('stream', renderYourWebCam);
				vidoCurrent.srcObject = stream;
				vidoCurrent.play();
			})
			.catch((err) => {
				console.log({ err });
			});
	});

	const handleConnect = async () => {
		console.log("button clicked")
		const conn = peer.connect(codeid);
		conn.on('data', (data) => {
			console.log('new data ' + data);
		});
		conn.on('open', () => {
			conn.send('hi');
		});
		conn.on("error", (err) => {
			console.log({err})
		})
		await navigator.mediaDevices
			.getUserMedia({
				video: true,
				audio: true
			})
			.then((stream) => {
				let call = peer.call(codeid, stream);
				vidoCurrent.srcObject = stream;
				vidoCurrent.play();
				call.on('stream', renderYourWebCam);
			})
			.catch((err) => {
				console.log({ err });
			});
	};
</script>

<svelte:head>
	<title>Video Call Api</title>
	<meta name="description" content="Video Call Api" />
</svelte:head>

<h2>Your id: {yourId}</h2>
<label for="">code :</label>
<input type="text" bind:value={codeid} />
<button on:click={handleConnect}>connect</button>
<div>
	<video width="400" height="400" autoplay={true} bind:this={vidoCurrent}>
		<track kind="captions" src="" />
	</video>
	<video width="400" height="400" autoplay={true} bind:this={videoElm}>
		<track kind="captions" src="" />
	</video>
</div>

<style>
	div {
		display: flex;
		gap: 1rem;
	}
</style>