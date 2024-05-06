let wpRequire;
window.webpackChunkdiscord_app.push([[ Math.random() ], {}, (req) => { wpRequire = req; }]);

let ApplicationStreamingStore = Object.values(wpRequire.c).find(x => x?.exports?.default?.getStreamerActiveStreamMetadata).exports.default;
let QuestsStore = Object.values(wpRequire.c).find(x => x?.exports?.default?.getQuest).exports.default;
let FluxDispatcher = Object.values(wpRequire.c).find(x => x?.exports?.default?.flushWaitQueue).exports.default;

let quest = [...QuestsStore.quests.values()].find(x => x.userStatus?.enrolledAt && !x.userStatus?.completedAt && new Date(x.config.expiresAt).getTime() > Date.now())
let isApp = navigator.userAgent.includes("Electron/")
if(!isApp) {
	console.log("This no longer works in browser. Use the desktop app!")
} else if(!quest) {
	console.log("You don't have any uncompleted quests!")
} else {
	let pid = Math.floor(Math.random() * 30000) + 1000
	ApplicationStreamingStore.getStreamerActiveStreamMetadata = () => ({
		id: quest.config.applicationId,
		pid,
		sourceName: null
	})
	
	let secondsNeeded = quest.config.streamDurationRequirementMinutes * 60
	let fn = data => {
		let progress = data.userStatus.streamProgressSeconds
		console.log(`Quest progress: ${progress}/${secondsNeeded}`)
		
		if(progress >= secondsNeeded) {
			console.log("Quest completed!")
			FluxDispatcher.unsubscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
		}
	}
	FluxDispatcher.subscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
	
	console.log(`Spoofed your stream to ${quest.config.applicationName}. Stay in vc for ${Math.ceil(quest.config.streamDurationRequirementMinutes - (quest.userStatus?.streamProgressSeconds ?? 0) / 60)} more minutes.`)
	console.log("Remember that you need at least 1 other person to be in the vc!")
}
