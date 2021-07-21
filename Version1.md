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
	<div id="complication1">Complication 1</div>
	<div id="complication2">Complication 2</div>
	<div id="complication3">Complication 3</div>
</div>

<div class="complication-list">
	<div id="complication4">Complication 4</div>
	<div id="complication5">Complication 5</div>
	<div id="complication6">Complication 6</div>
</div>

<p>Easy: 1-2 complications, Medium: 3-4 complications, Hard: 5-6 complications</p>

Default configuration: <button id="easy" onclick="setComplicationCount(2)">Easy</button> <button id="medium" onclick="setComplicationCount(4)">Easy</button> <button id="hard" onclick="setComplicationCount(6)">Hard</button>

Number of complications: <input id="complicationCount" type="number" value="6" min="1" max="6" onchange="generate()"><br>
<button onclick="generate()">Generate Complications</button>


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
const complicationEls = [
	document.getElementById('complication1'),
	document.getElementById('complication2'),
	document.getElementById('complication3'),
	document.getElementById('complication4'),
	document.getElementById('complication5'),
	document.getElementById('complication6'),
];

function getRandomComplication() {
	return complications[Math.floor(Math.random() * complications.length)];
}

function generate() {
	const count = getComplicationCount();
	const choices = complications;
	const picks = complications
		.map((value) => ({ value, sort: Math.random() }))
		.sort((a, b) => a.sort - b.sort)
		.map(({ value }) => value)
		.slice(0, count);
	
	for (let i = 0; i < picks.length; ++i) {
		complicationEls[i].textContent = picks[i];
	}
	for (; i < complicationEls.length; ++i) {
		complicationEls[i].textContent = '';
	}
}
	
function setComplicationCount(number) {
	const complicationCount = document.getElementById('complicationCount');
	complicationCount.value = number;
}
	
function getComplicationCount() {
	return parseInt(document.getElementById('complicationCount').value);
}

generate();
</script>
