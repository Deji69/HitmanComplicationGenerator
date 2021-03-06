<head>
	<title>Hitman Complication Generator</title>
	<style>
.complication-list {
	margin: 1em;
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
	align-items: center;
}
.complication-list>*,#complication-list>* {
	margin: 10px 0;
	padding: 0 15px;
	font-weight: bold;
	color: #000099;
	text-decoration: underline;
	text-decoration-style: dotted;
}
.complication-list>*>* {
	padding: 8px;
	background: #E0E9FF;
	border-radius: 10px;
	white-space: nowrap;
}
</style></head>

<p class="info">Complete the mission with the following restrictions. You can kill targets with any method or disguise that doesn't violate a restriction.</p>

<div class="complication-list">
	<div><div id="complication1">Complication 1</div></div>
	<div><div id="complication2">Complication 2</div></div>
	<div><div id="complication3">Complication 3</div></div>
	<div><div id="complication4">Complication 4</div></div>
	<div><div id="complication5">Complication 5</div></div>
	<div><div id="complication6">Complication 6</div></div>
</div>

<div class="controls">
	<div>Presets: <button id="easy" onclick="setComplicationCount(2)">Easy</button> <button id="medium" onclick="setComplicationCount(4)">Medium</button> <button id="hard" onclick="setComplicationCount(6)">Hard</button></div>
	<div>Number of complications: <input id="complicationCount" type="number" value="6" min="1" max="6" onchange="generate()"></div>
	<div>Loadout: <input id="enableLoadout" type="checkbox" onchange="generate()" checked></div>
	<div>Objectives: <input id="enableObjectives" type="checkbox" onchange="generate()"></div>
	<div><button onclick="generate()">Generate Complications</button></div>
</div>

<div class="info">
	<ul>
		<li>Loadout complications only apply to loadout, not items in the map.</li>
		<li>'Gear' refers to the gear slots in your loadout.</li>
		<li>'Item' refers to both smuggled items and 'gear' slot items.</li>
	</ul>

	<h4>Possible Complications</h4>
	<ul id="complication-list"></ul>
</div>

<script>
const complications = [
	{
		name: 'Default Start',
		hint: 'NG default location or closest NG+ start',
		exclude: ['Disguise Start Only', 'Suit Start Only'],
	},
	{
		name: 'Disguise Start Only',
		hint: 'No suit starting locations',
		exclude: ['Default Start', 'Suit Start Only'],
	},
	{
		name: 'Suit Start Only',
		hint: 'Only suit starting locations.',
		exclude: ['Disguise Start Only', 'Default Start'],
	},
	{
		name: 'Free Disguises Only',
		hint: 'No taking disguises from NPCs, only disguises lying around.',
		exclude: ['No Free Disguises', 'No Disguise Changes'],
	},
	{
		name: 'No Free Disguises',
		hint: 'Not allowed to use any disguises lying around, only ones from NPCs or starting location.',
		exclude: ['Free Disguises Only', 'No Disguise Changes'],
	},
	{
		name: 'No Disguise Changes',
		hint: 'Remain in the disguise you start the level with.',
		exclude: ['Free Disguises Only', 'No Free Disguises', 'Suit/Disguise Retrieval'],
	},
	{
		name: 'No KOs',
		hint: 'Do not pacify any NPC in any way, including targets.',
		exclude: ['No Female KOs', 'No Target KOs'],
	},
	{
		name: 'No Female KOs',
		hint: 'Do not pacify any female NPC in any way, including targets.',
		exclude: ['No KOs', 'No Male KOs', 'No Target KOs'],
	},
	{
		name: 'No Male KOs',
		hint: 'Do not pacify any male NPC in any way, including targets.',
		exclude: ['No KOs', 'No Female KOs', 'No Target KOs'],
	},
	{
		name: 'No Target KOs',
		hint: 'Do not KO the targets before killing.',
		exclude: ['No KOs'],
	},
	{
		name: 'No Emetics',
		hint: 'No making NPCs sick in any way.',
	},
	{
		name: 'No Live Non-Lethal Shots (Panic)',
		hint: 'Body shots on NPCs are allowed only when they are unconscious or when the shot kills them, so no using it for panic strats (3 shot still ok).',
	},
	{
		name: 'No Falling Object Kills',
		hint: 'No killing targets with chandeliers or other falling objects.',
	},
	{
		name: 'No Propane Kills',
		hint: 'Targets cannot be killed with propane. You may still blow up propane for panics etc.',
	},
	{
		name: 'No Body Dumping',
		hint: 'No hiding of bodies in cupboards or freezers etc. Hiding in grass is still fine. Push prompt on live NPCs is fine.',
		exclude: ['No Body Dragging'],
	},
	{
		name: 'No Body Dragging',
		hint: 'No dragging bodies of KO\'d or killed NPCs. Yes, this also means no hiding bodies unless they land in a hiding spot.',
		exclude: ['No Body Dumping'],
	},
	{
		name: 'No Loud Explosions (excludes breaching charges)',
		hint: 'No causing explosions at all, including fire extinguishers. Keep it quiet, no bomb threat alerts.',
	},
	{
		name: 'No Ladder or Pipe Climbing',
		hint: 'Vaulting and ledge shimmying is okay because I\'m not a sadist and I value your patience.',
	},
	{
		name: 'No Throwing',
		hint: 'No throwing any items, even at people\'s heads. Dropping or placing them is okay.',
		exclude: ['No Placing or Dropping'],
	},
	{
		name: 'No Placing or Dropping',
		hint: 'No placing or dropping items or weapons. Throwing is okay. Enjoy the extra attention.',
		exclude: ['No Throwing'],
	},
	{
		name: 'Suit/Disguise Retrieval',
		hint: 'Leave in the same suit you started in. If you started in a disguise, any identical disguise is acceptable.',
		exclude: ['No Disguise Changes'],
	},
	{
		name: 'Loadout: No ICA Titanium Crowbar or Lockpicks',
		hint: 'Choose another way through locked doors or find a crowbar in the map.',
	},
	{
		name: 'Loadout: No Tasers or EMP',
		hint: 'No bringing those items that spark in your loadout.',
	},
	{
		name: 'Loadout: No Syringes',
		hint: 'No bringing syringe poison items in your loadout.',
	},
	{
		name: 'Loadout: No Consumed Poisons',
		hint: 'No bringing consumed poison items in your loadout.',
	},
	{
		name: 'Loadout: No Silenced Guns',
		hint: 'Unsuppressed guns only. Includes dart guns.',
		exclude: ['Loadout: No Dart Guns'],
	},
	{
		name: 'Loadout: No Automatic Guns',
		hint: 'No SMGs or F/A pistols in loadout. Find another way to get your doors open.',
	},
	{
		name: 'Loadout: No Dart Guns',
		hint: 'No dart guns in loadout.',
		exclude: ['Loadout: No Silenced Guns'],
	},
	{
		name: 'Loadout: Frisk-Safe Guns Only',
		hint: 'Only bring guns that are frisk safe. Applies concealed weapon, gear slots and smuggles. Using guns in the map is fine.',
	},
	{
		name: 'Loadout: Explosive Items Only',
		hint: 'Only allowed to bring explosive items in your gear slots and smuggles. You can still bring a weapon in your weapon slot. Includes breaching charges and anything else that creates a real explosion (no flash devices or goldbrick proximity mine, etc.).',
	},
	/*{
		name: 'Loadout: Audio Luring Items Only',
		hint: 'Only bring items that will casually lure NPCs via audio when placed or detonated e.g. audio devices, flash phone, napoleon, goldbrick proximity mine (snail). Includes breaching charge! You don\'t have to lure anyone with them though.',
	},*/
	{
		name: 'Loadout: No Illegal Items',
		hint: 'No bringing items that are illegal to hold. You may still bring micro devices and items that are only illegal to throw (e.g. micro taser, golf ball). Does not apply to the concealed weapon slot.',
		exclude: ['Loadout: No Legal Items'],
	},
	{
		name: 'Loadout: No Legal Items',
		hint: 'Only bring items that are illegal to hold. Does not apply to the concealed weapon slot.',
		exclude: ['Loadout: No Illegal Items'],
	},
	{
		name: 'Loadout: No Agency Pickup / Stash Only',
		hint: 'Only use stash smuggles, not agency pickups.',
		exclude: ['Loadout: No Stash / Agency Pickup Only'],
	},
	{
		name: 'Loadout: No Stash / Agency Pickup Only',
		hint: 'Only use agency pickup smuggles, not stashes.',
		exclude: ['Loadout: No Agency Pickup / Stash Only'],
	},
	{
		name: 'Loadout: No Gear Briefcase',
		hint: 'No bringing a briefcase in the Gear slot.',
	},
	{
		name: 'Loadout: One Gear Slot',
		hint: 'Only allowed a single \'Gear\' slot item, leave the other one empty.',
		exclude: ['Loadout: No Gear'],
	},
	{
		name: 'Loadout: No Gear',
		hint: 'The only item you can bring will be via a smuggle point, not in your gear slots. A concealed weapon is not an \'item\'.',
		exclude: ['Loadout: One Gear Slot'],
	},
	{
		name: 'Loadout: No Concealed Weapon',
		hint: 'Cannot bring a weapon in the concealed weapon slot. Sacrifice a gear slot or go to an ICA drop if you need a gun.',
	},
	{
		name: 'Objective: Delete Recordings',
		hint: 'Destroy the recordings by shooting or interacting with the recorder.',
	},
	{
		name: 'Objective: Raise Alarm',
		hint: 'Trigger an alarm using a fire alarm panel.',
	},
	{
		name: 'Objective: Steal a Knife',
		hint: 'Steal any kind of knife from the level and exit with it.',
	},
	{
		name: 'Objective: Steal a Large Lethal Melee',
		hint: 'Steal any a large unconcealable lethal melee object in the level and exit with it.',
	},
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
	
function renderComplicationList() {
	const ul = document.getElementById('complication-list');
	ul.innerHTML = '';
	for (let k in complications) {
		let li = document.createElement('li');
		li.textContent = complications[k].name;
		li.title = complications[k].hint;
		ul.appendChild(li);
	}
}

function generate() {
	const count = getComplicationCount();
	const objectives = getObjectivesEnabled();
	const loadout = getLoadoutEnabled();
	const choices = complications;
	const exclude = [];
	
	renderComplicationList();
	
	let picks = complications
		.map((value) => ({ value, sort: Math.random() }))
		.sort((a, b) => a.sort - b.sort)
		.map(({ value }) => value);
	
	for (let i = 0; i < picks.length && i < count; ++i) {
		const pick = picks[i];
		
		if (exclude.includes(pick.name) || (!objectives && pick.name.startsWith('Objective:')) || (!loadout && pick.name.startsWith('Loadout:'))) {
			picks.splice(i--, 1);
		} else {
			if (typeof pick.exclude !== 'undefined') {
				exclude.push(...pick.exclude);
			}
		}
	}
	
	picks.splice(count);
	
	picks = picks.sort((a, b) => {
		const av = a.name.startsWith('Objective:') ? 2 : a.name.startsWith('Loadout:') ? 1 : 0;
		const bv = b.name.startsWith('Objective:') ? 2 : b.name.startsWith('Loadout:') ? 1 : 0;
		return av + -bv;
	});
	
	for (let i = 0; i < complicationEls.length; ++i) {
		if (i < picks.length) {
			complicationEls[i].textContent = picks[i].name;
			complicationEls[i].title = picks[i].hint;
		} else {
			complicationEls[i].textContent = '';
			complicationEls[i].title = '';
		}
	}
}
	
function setComplicationCount(number) {
	const complicationCount = document.getElementById('complicationCount');
	complicationCount.value = number;
}
	
function getComplicationCount() {
	return parseInt(document.getElementById('complicationCount').value);
}
	
function getObjectivesEnabled() {
	return document.getElementById('enableObjectives').checked;
}
	
function getLoadoutEnabled() {
	return document.getElementById('enableLoadout').checked;
}

generate();
</script>
