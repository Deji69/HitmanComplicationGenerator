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

Presets: <button id="easy" onclick="setComplicationCount(2)">Easy</button> <button id="medium" onclick="setComplicationCount(4)">Easy</button> <button id="hard" onclick="setComplicationCount(6)">Hard</button>

Number of complications: <input id="complicationCount" type="number" value="6" min="1" max="6" onchange="generate()"><br>
<button onclick="generate()">Generate Complications</button>


<script>
const complications = [
	{
		name: 'Default Loadout',
		hint: 'ICA19, Fibre Wire, Coins, no ICA pickup',
	},
	{
		name: 'Default Start',
		hint: 'NG default location or closest NG+ start',
	},
	{
		name: 'Disguise Start Only',
		hint: 'No suit starting locations',
	},
	{
		name: 'Suit Start Only',
		hint: 'Only suit starting locations.',
	},
	{
		name: 'Free Disguises Only',
		hint: 'No taking disguises from NPCs, only disguises lying around.'
	},
	{
		name: 'No Free Disguises',
		hint: 'Not allowed to use any disguises lying around, only ones from NPCs or starting location.',
	},
	{
		name: 'No Disguise Changes',
		hint: 'Remain in the disguise you start the level with.',
	},
	{
		name: 'No KOs',
		hint: 'Do not pacify any NPC in any way, including targets.',
	},
	{
		name: 'No Target KOs (live kills only)',
		hint: 'Do not KO the targets before killing.',
	},
	{
		name: 'No Shooting',
		hint: 'Bringing a weapon is okay, but you're not allowed to fire it.',
	},
	{
		name: 'No Emetics',
		hint: 'No making NPCs sick in any way.'
	},
	{
		name: 'No Dart Guns',
		hint: 'No dart guns in loadout.',
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
		hint: 'No hiding of bodies in cupboards or freezers etc. Hiding in grass is still fine.',
	},
	{
		name: 'No Body Dragging',
		hint: 'No dragging bodies of KO'd or killed NPCs. Yes, this also means no hiding bodies unless they land in a hiding spot.',
	},
	{
		name: 'No Loud Explosions (excludes breaching charges)',
		hint: 'No causing explosions at all, including fire extinguishers. Keep it quiet, no bomb threat alerts.',
	},
	{
		name: 'No Ladder or Pipe Climbing',
		hint: 'Vaulting and ledge shimmying is okay because I'm not a sadist and I value your patience.',
	},
	{
		name: 'No Throwing',
		hint: 'No throwing any items, even at people\'s heads. Dropping or placing them is okay.',
	},
	{
		name: 'No Placing or Dropping',
		hint: 'No placing or dropping items or weapons. Throwing is okay. Enjoy the extra attention.',
	},
	{
		name: 'Loadout: No Silenced Guns',
		hint: 'Unsuppressed guns only. Includes dart guns.',
	},
	{
		name: 'Loadout: No Automatic Weapons',
		hint: 'No SMGs or F/A pistols in loadout. Find another way to get your doors open.',
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
		name: 'Loadout: Explosive Items Only (weapon slot still allowed)',
		hint: 'Only allowed to bring explosive items in your item slots and ICA drops. You can still bring a weapon in your weapon slot. Includes breaching charges and anything else that creates a real explosion (no flash devices or goldbrick proximity mine, etc.).',
	}
	{
		name: 'Loadout: \'Legal\' Items Only',
		hint: 'No bringing items that are illegal to hold. You may still bring micro devices and items that are only illegal to throw (e.g. micro taser, golf ball). Only applies to item slots, not the weapon slot.',
	},
	{
		name: 'Loadout: Concealable Weapon Only',
		hint: 'Only bring guns in the weapon slot or ICA drops that can pass frisk detection.',
	},
	{
		name: 'Loadout: Concealable Items Only',
		hint: 'Only bring items in the item slots or ICA drops that can pass frisk detection.',
	},
	{
		name: 'Loadout: Audio Luring Items Only',
		hint: 'Only bring items that will casually lure NPCs via audio when placed or detonated e.g. audio devices, flash phone, napoleon, goldbrick proximity mine (snail). Includes breaching charge! You don\'t have to lure anyone with them though.',
	},
	{
		name: 'Loadout: No Briefcases or Agency Pickups',
		hint: 'No bringing briefcases in loadout or via agency pickups. Hidden stashes only.',
	},
	{
		name: 'Loadout: No Stashes / Agency Pickups Only',
		hint: 'Can only use Agency Pickups locations to smuggle in items, not stashes.',
	},
	{
		name: 'Loadout: No Gear / Smuggle Item Only',
		hint: 'The only item you can bring will be via a smuggle point, not in your gear slots.'
	},
	{
		name: 'Loadout: No Concealed Weapon or Gear Weapon / Smuggle Weapon Only',
		hint: 'The only weapon you can bring will be via a smuggle point, not in your concealed weapon slot or your gear slot.'
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

function generate() {
	const count = getComplicationCount();
	const choices = complications;
	const exclude = [];
	const picks = complications
		.map((value) => ({ value, sort: Math.random() }))
		.sort((a, b) => a.sort - b.sort)
		.map(({ value }) => value);
	
	for (let i = 0; i < picks.length && i < count; ++i) {
		const pick = picks[i];
		
		if (exclude.contains(picks.name)) {
			picks.splice(i--);
		} else {
			if (typeof picks.exclude !== 'undefined' && picks.exclude) {
				exclude.push(...picks.exclude);
			}
		}
	}
	
	for (; i < complicationEls.length; ++i) {
		complicationEls[i].textContent = i < count ? picks.length : '';
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
