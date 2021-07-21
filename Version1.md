<style>
.complication-list {
	display: flex;
	flex-direction: row;
	justify-content: space-around;
	margin: 1em;
}
.complication-list>* {
	padding: 0 15px;
	font-weight: bold;
	color: #000099;
}
</style>

<h1>Hitman Complication Generator</h1>

<p>You can kill the target(s) with any method or disguise you choose, so long as it does not violate the restrictions...</p>

<div class="complication-list">
	<div id="complication1">No Complication</div>
	<div id="complication2">No Complication</div>
	<div id="complication3">No Complication</div>
</div>

<p>Easy: 1 complication, Medium: 2 complications, Hard: 3 complications</p>

<script>
const complications = [
	'Default Loadout (ICA19, Fibre Wire, Coins)',
	'Default Start',
	'Disguise Start Only',
	'Suit Start Only',
	'Suit Only',
	'Free Disguises Only',
	'No Free Disguises (disguise start ok)',
	'No KOs',
	'No Target KOs (live kills only)',
	'No Shooting',
	'No Emetics',
	'No Dart Guns',
	'No Falling Objects',
	'No Propane',
	'No Silenced Guns',
	'No Body Dumping',
	'No Explosions',
	'No Ladder or Pipe Climbing (vaulting, ledge shimmy etc. is ok)',
	'No Item Throwing',
	'No Tasers or EMP',
	'Loadout: Explosive Items Only (weapon slot still allowed)',
	'Loadout: \'Legal\' Items Only',
];

function getRandomComplication() {
	return complications[Math.floor(Math.random() * complications.length)];
}

function generate() {
	const complication1 = document.getElementById('complication1');
	const complication2 = document.getElementById('complication2');
	const complication3 = document.getElementById('complication3');
	let c1 = getRandomComplication();
	let c2 = c1;
	let c3 = c1;
	
	do {
		c2 = getRandomComplication();
	} while (c2 === c1);
	
	do {
		c3 = getRandomComplication();
	} while (c3 === c1 || c3 === c2);
	
	complication1.textContent = c1;
	complication2.textContent = c2;
	complication3.textContent = c3;
}

generate();
</script>

<button onclick="generate()">Generate New Complications</button>
