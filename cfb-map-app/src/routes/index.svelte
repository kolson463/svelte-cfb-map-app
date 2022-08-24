<script type="ts">
	import ConferenceButton from '../components/ConferenceButton.svelte';
	import TeamListButton from '../components/TeamListButton.svelte';
	import axios from 'axios';
	import { load } from 'cheerio';
	import { createPopper, type VirtualElement } from '@popperjs/core';
	import mapboxgl from 'mapbox-gl';
	import { onMount } from 'svelte';
	import DarkMode from 'svelte-dark-mode';
	import { afterUpdate } from 'svelte';

	let theme: any;
	afterUpdate(() => {
		document.body.className = theme; // "dark" or "light"
	});

	let loadingStatus = false;

	let conferenceDropdownPopoverShow = false;
	let conferenceBtnDropdownRef: Element | VirtualElement;
	let conferencePopoverDropdownRef: HTMLElement;

	const conferenceToggleDropdown = () => {
		if (conferenceDropdownPopoverShow) {
			conferenceDropdownPopoverShow = false;
		} else {
			conferenceDropdownPopoverShow = true;
			createPopper(conferenceBtnDropdownRef, conferencePopoverDropdownRef, {
				placement: 'bottom-start'
			});
		}
	};

	let helpDropdownPopoverShow = false;
	let helpBtnDropdownRef: Element | VirtualElement;
	let helpPopoverDropdownRef: HTMLElement;

	const helpToggleDropdown = () => {
		if (helpDropdownPopoverShow) {
			helpDropdownPopoverShow = false;
		} else {
			helpDropdownPopoverShow = true;
			createPopper(helpBtnDropdownRef, helpPopoverDropdownRef, {
				placement: 'bottom-start'
			});
		}
	};

	let teamDropdownPopoverShow = false;
	let teamBtnDropdownRef: Element | VirtualElement;
	let teamPopoverDropdownRef: HTMLElement;

	const teamToggleDropdown = () => {
		if (teamDropdownPopoverShow) {
			teamDropdownPopoverShow = false;
		} else {
			teamDropdownPopoverShow = true;
			createPopper(teamBtnDropdownRef, teamPopoverDropdownRef, {
				placement: 'bottom-start'
			});
		}
	};

	type teamInfo = { name: string; slug: string; location: string };

	let B1GArray: teamInfo[] = [
		{ name: 'Illinois', slug: 'illinois', location: 'Champaign, IL' },
		{ name: 'Iowa', slug: 'iowa', location: 'Iowa City, IA' },
		{ name: 'Maryland', slug: 'maryland', location: 'College Park, MD' },
		{ name: 'Indiana', slug: 'indiana', location: 'Bloomington, IN' },
		{ name: 'Michigan State', slug: 'michigan-state', location: 'East Lansing, MI' },
		{ name: 'Michigan', slug: 'michigan', location: 'Ann Arbor, MI' },
		{ name: 'Minnesota', slug: 'minnesota', location: 'Minneapolis, MN' },
		{ name: 'Nebraska', slug: 'nebraska', location: 'Lincoln, NE' },
		{ name: 'Northwestern', slug: 'northwestern', location: 'Evanston, IL' },
		{ name: 'Ohio State', slug: 'ohio-state', location: 'Columbus, OH' },
		{ name: 'Penn State', slug: 'penn-state', location: 'State College, PA' },
		{ name: 'Purdue', slug: 'purdue', location: 'West Lafayette, IN' },
		{ name: 'Rutgers', slug: 'rutgers', location: 'New Brunswick, NJ' },
		{ name: 'Wisconsin', slug: 'wisconsin', location: 'Madison, WI' }
	];

	let ACCArray: teamInfo[] = [
		{ name: 'Boston College', slug: 'boston-college', location: 'Chestnut Hull, MA' },
		{ name: 'Clemson', slug: 'clemson', location: 'Clemson, SC' },
		{ name: 'Duke', slug: 'duke', location: 'Durham, NC' },
		{ name: 'Florida State', slug: 'florida-state', location: 'Tallahassee, FL' },
		{ name: 'Georgia Tech', slug: 'georgia-tech', location: 'Atlanta, GA' },
		{ name: 'Louisville', slug: 'louisville', location: 'Louisville, KY' },
		{ name: 'Miami', slug: 'miami', location: 'Coral Gables, FL' },
		{ name: 'NC State', slug: 'nc-state', location: 'Raleigh, NC' },
		{ name: 'North Carolina', slug: 'north-carolina', location: 'Chapel Hill, NC' },
		{ name: 'Pittsburgh', slug: 'pittsburgh', location: 'Pittsburgh, PA' },
		{ name: 'Virginia', slug: 'virginia', location: 'Charlottesville, VA' },
		{ name: 'Wake Forest', slug: 'wake-forest', location: 'Winston-Salem, NC' },
		{ name: 'Virginia Tech', slug: 'virgina-tech', location: 'Blacksburg, VA' }
	];

	let SECArray: teamInfo[] = [
		{ name: 'Alabama', slug: 'alabama', location: 'Tuscaloosa, AL' },
		{ name: 'Texas A&M', slug: 'texas-am', location: 'College Station, TX' },
		{ name: 'Arkansas', slug: 'arkansas', location: 'Fayetteville, AR' },
		{ name: 'Missouri', slug: 'missouri', location: 'Columbia, MO' },
		{ name: 'Kentucky', slug: 'kentucky', location: 'lexington, KY' },
		{ name: 'Vanderbilt', slug: 'vanderbilt', location: 'Nashville, TN' },
		{ name: 'Tennessee', slug: 'tennessee', location: 'Knoxville, TN' },
		{ name: 'Georgia', slug: 'georgia', location: 'Athens, GA' },
		{ name: 'Florida', slug: 'florida', location: 'Gainesville, FL' },
		{ name: 'Mississippi State', slug: 'mississippi-state', location: 'Starkville, MS' },
		{ name: 'Ole Miss', slug: 'ole-miss', location: 'Oxford, MS' },
		{ name: 'South Carolina', slug: 'south-carolina', location: 'Columbia, SC' },
		{ name: 'Auburn', slug: 'auburn', location: 'Auburn, AL' }
	];

	let Big12Array: teamInfo[] = [
		{ name: 'Baylor', slug: 'baylor', location: 'Waco, TX' },
		{ name: 'Iowa State', slug: 'iowa-state', location: 'Ames, IA' },
		{ name: 'Kansas', slug: 'kansas', location: 'Lawrence, KS' },
		{ name: 'Kansas State', slug: 'kansas-state', location: 'Manhattan, KS' },
		{ name: 'Oklahoma', slug: 'oklahoma', location: 'Norman, OK' },
		{ name: 'Oklahoma State', slug: 'oklahoma-state', location: 'Stillwater, OK' },
		{ name: 'TCU', slug: 'tcu', location: 'Forth Worth, TX' },
		{ name: 'Texas', slug: 'texas', location: 'Austin, TX' },
		{ name: 'Texas Tech', slug: 'texas-tech', location: 'Lubbock, TX' },
		{ name: 'West Virginia', slug: 'west-virginia', location: 'Morgantown, WV' }
	];

	let Pac12Array: teamInfo[] = [
		{ name: 'Colorado', slug: 'colorado', location: 'Boulder, CO' },
		{ name: 'Utah', slug: 'utah', location: 'Salt Lake City, UT' },
		{ name: 'Washington State', slug: 'washington-state', location: 'Pullman, WA' },
		{ name: 'Washington', slug: 'washington', location: 'Seattle, WA' },
		{ name: 'Oregon', slug: 'oregon', location: 'Eugene, OR' },
		{ name: 'Oregon State', slug: 'oregon-state', location: 'Corvallis, OR' },
		{ name: 'California', slug: 'california', location: 'Berkeley, CA' },
		{ name: 'UCLA', slug: 'ucla', location: 'Beverly Hills, CA' },
		{ name: 'USC', slug: 'usc', location: 'Los Angeles, CA' },
		{ name: 'Arizona State', slug: 'arizona-state', location: 'Tempe, AZ' },
		{ name: 'Arizona', slug: 'arizona', location: 'Tucson, AZ' },
		{ name: 'Stanford', slug: 'stanford', location: 'Stanford, CA' }
	];

	let AACArray: teamInfo[] = [
		{ name: 'Cincinnati', slug: 'cincinnati', location: 'Cincinnati, OH' },
		{ name: 'East Carolina', slug: 'east-carolina', location: 'Greenville, NC' },
		{ name: 'Houston', slug: 'houston', location: 'Houston, TX' },
		{ name: 'Memphis', slug: 'memphis', location: 'memphis, TN' },
		{ name: 'Navy', slug: 'navy', location: 'Annapolis, MD' },
		{ name: 'SMU', slug: 'smu', location: 'Dallas, TX' },
		{ name: 'South Florida', slug: 'south-florida', location: 'Tampa, FL' },
		{ name: 'Temple', slug: 'temple', location: 'Philadelphia, PA' },
		{ name: 'Tulane', slug: 'tulane', location: 'New Orleans, LA' },
		{ name: 'Tulsa', slug: 'tulsa', location: 'Tulsa, OK' },
		{ name: 'UCF', slug: 'central-florida', location: 'Orlando, FL' }
	];

	let INDArray: teamInfo[] = [
		{ name: 'Army', slug: 'army', location: 'West Point, NY' },
		{ name: 'BYU', slug: 'byu', location: 'Provo, UT' },
		{ name: 'Notre Dame', slug: 'notre-dame', location: 'South Bend, IN' }
	];

	let CUSAArray: teamInfo[] = [
		{ name: 'Charlotte', slug: 'charlotte', location: 'Charlotte, NC' },
		{ name: 'Florida International', slug: 'florida-international', location: 'Miami, FL' },
		{ name: 'Florida Atlantic', slug: 'florida-atlantic', location: 'Boca Raton, FL' },
		{ name: 'Louisiana Tech', slug: 'louisiana-tech', location: 'Ruston, LA' },
		{ name: 'Middle Tennessee State', slug: 'middle-tennessee-state', location: 'Murfreesboro, TN' },
		{ name: 'North Texas', slug: 'north-texas', location: 'Denton, TX' },
		{ name: 'Rice', slug: 'rice', location: 'Houston, TX' },
		{ name: 'Alabama Birmingham', slug: 'alabama-birmingham', location: 'Birmingham, AL' },
		{ name: 'UTEP', slug: 'utep', location: 'El Paso, TX' },
		{ name: 'Western Kentucky', slug: 'western-kentucky', location: 'Bowling Green, KY' },
		{ name: 'UTSA', slug: 'utsa', location: 'San Antonio, TX' }
	];

	let MACArray: teamInfo[] = [
		{ name: 'Akron', slug: 'akron', location: 'Akron, OH' },
		{ name: 'Ball State', slug: 'ball-state', location: 'Muncie, IN' },
		{ name: 'Bowling Green', slug: 'bowling-green', location: 'Bowling Green, OH' },
		{ name: 'Buffalo', slug: 'buffalo', location: 'Buffalo, NY' },
		{ name: 'Central Michigan', slug: 'Central Michigan', location: 'Mount Pleasant, MI' },
		{ name: 'Eastern Michigan', slug: 'eastern-michigan', location: 'Ypsilanti, MI' },
		{ name: 'Kent State', slug: 'kent-state', location: 'Kent, OH' },
		{ name: 'Miami Ohio', slug: 'miami-ohio', location: 'Oxford, Ohio' },
		{ name: 'Northern Illinois', slug: 'northern-illinois', location: 'Dekalb, IL' },
		{ name: 'Ohio', slug: 'ohio', location: 'Athens, OH' },
		{ name: 'Toledo', slug: 'toldeo', location: 'Toledo, OH' },
		{ name: 'Western Michigan', slug: 'western-michigan', location: 'Kalamazoo, MI' }
	];

	let MWESTArray: teamInfo[] = [
		{ name: 'Air Force', slug: 'air-force', location: 'Colorado Springs, CO' },
		{ name: 'Boise State', slug: 'boise-state', location: 'Boise, ID' },
		{ name: 'Colorado State', slug: 'colorado-state', location: 'Fort Collins, CO' },
		{ name: 'Fresno State', slug: 'fresno-state', location: 'Fresno, CA' },
		{ name: 'Hawaii', slug: 'hawaii', location: 'Honolulu, HI' },
		{ name: 'Nevada', slug: 'nevada', location: 'Reno, NV' },
		{ name: 'New Mexico', slug: 'New Mexico', location: 'Albuquerque, NM' },
		{ name: 'San Diego State', slug: 'san-diego-state', location: 'San Diego, CA' },
		{ name: 'San Jose State', slug: 'san-jose-state', location: 'San Jose, CA' },
		{ name: 'UNLV', slug: 'unlv', location: 'Las Vegas, NV' },
		{ name: 'Utah State', slug: 'utah-state', location: 'Logan, UT' },
		{ name: 'Wyoming', slug: 'wyoming', location: 'Laramie, WY' }
	];

	let SBCArray: teamInfo[] = [
		{ name: 'Appalachian State', slug: 'appalachian-state', location: 'Boone, NC' },
		{ name: 'Arkansas State', slug: 'arkansas-state', location: 'Jonesboro, AR' },
		{ name: 'Coastal Carolina', slug: 'coastal-carolina', location: 'Conway, SC' },
		{ name: 'Georgia Southern', slug: 'georgia-southern', location: 'Statesboro, GA' },
		{ name: 'Georgia State', slug: 'georgia-state', location: 'Atlanta, GA' },
		{ name: 'James Madison', slug: 'james-madison', location: 'Harrisonburg, VA' },
		{ name: 'Louisiana-Monroe', slug: 'louisian-monroe', location: 'Monroe, LA' },
		{ name: 'Louisiana', slug: 'louisiana', location: 'Lafayette, LA' },
		{ name: 'Marshall', slug: 'marshall', location: 'Hungtington, WV' },
		{ name: 'Old Dominion', slug: 'old-dominion', location: 'Norfolk, VA' },
		{ name: 'South Alabama', slug: 'south-alabama', location: 'Mobile, AL' },
		{ name: 'Southern Miss', slug: 'southern-miss', location: 'Hattiesburg, MS' },
		{ name: 'Texas State', slug: 'texas-state', location: 'San Marcos, TX' },
		{ name: 'Troy', slug: 'troy', location: 'Troy, AL' }
	];

	mapboxgl.accessToken = import.meta.env.VITE_MAPBOX_ACCESS_TOKEN;

	
	let [team_long, team_lat] = [-96.681679, 40.806]; //Lincoln, NE

	async function updateTeamLocation() {
		if (selectedConference === 'B1G') {
			B1GArray.forEach(async (team) => {
				if (team.name === selectedTeam) {
					//console.log(team.location);
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
					//console.log([team_long, team_lat]);
				}
			});
		} else if (selectedConference === 'SEC') {
			SECArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'ACC') {
			ACCArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'CUSA') {
			CUSAArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'AAC') {
			AACArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'Big-12') {
			Big12Array.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'Pac-12') {
			Pac12Array.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'SBC') {
			SBCArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'MAC') {
			MACArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'MWEST') {
			MWESTArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else {
			INDArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		}
	}

	async function geoCodeLocations(selectedLocation: string): Promise<[number, number]> {
		let parsedLocation = selectedLocation;
		//console.log('Parsed: ' + parsedLocation + 'SelectedLocation: ' + selectedLocation);
		if (selectedLocation.includes('(Prep)')) {
			parsedLocation = selectedLocation.replace('(Prep)', ' ');
			//console.log(parsedLocation);
		}

		if (parsedLocation.includes('(')) {
			//Prep School Adjustments
			var init = parsedLocation.indexOf('(');
			var fin = parsedLocation.indexOf(')');
			parsedLocation = parsedLocation.substr(init + 1, fin - init - 1);
			//console.log('Parsed: ' + parsedLocation);
		}

		const geoCodeUrl =
			'https://api.mapbox.com/geocoding/v5/mapbox.places/' +
			parsedLocation +
			'.json?limit=1&autocomplete=false&access_token=pk.eyJ1Ijoia29sc29uNDYzIiwiYSI6ImNsNzZ0NDVqYTBpcnEzbnFodHE5bjVyN2IifQ.u9hrRp9nzRbiGT3mJ-X5LA';

			

		try {
			const teamCoordsRaw = await axios.get(geoCodeUrl);
			//console.log(teamCoordsRaw);
			const coords = teamCoordsRaw.data.features[0].center;
			//console.log(coords);
			return coords;
		} catch (error) {
			console.log(error);
			return [40, 40];
		}
	}

	var teamMarkers:
		| {
				togglePopup(): any;
				remove: () => void;
		  }[]
		| null = [];

	var playerMarkers:
		| {
				marker: mapboxgl.Marker;
				id: string;
				togglePopup(): any;
				remove: () => void;
		  }[]
		| null = [];

	var maps:
		| {
				getPopup(): () => void;
				flyTo(arg0: { center: number[] }): any;
				remove: () => void;
		  }[]
		| null = [];

	function removeTeamMarkers() {
		if (teamMarkers !== null) {
			for (var i = teamMarkers.length - 1; i >= 0; i--) {
				teamMarkers[i].remove();
			}
		}
	}

	function removeMaps() {
		if (maps !== null) {
			for (var i = maps.length - 1; i >= 0; i--) {
				maps[i].remove();
			}
		}
	}

	onMount(async () => {
		await getCommits();
		await commitsToMap();
	});

	function commitCenter(playerCoords: [number, number], playerName: string) {
		if (maps !== null) {
			for (var i = maps.length - 1; i >= 0; i--) {
				maps[i].flyTo({
					center: playerCoords
				});
			}

			if (playerMarkers !== null) {
				var i = playerMarkers.length - 1;
				while (playerName !== playerMarkers[i].id) {
					//console.log('Player Name ' + playerName);
					//console.log('Player Marker Id ' + playerMarkers[i].id);
					i--;
				}
				if (playerName === playerMarkers[i].id) {
					playerMarkers[i].marker.togglePopup();
				}
			}
		}
	}

	function teamCenter(teamCoords: [number, number]) {
		if (maps !== null) {
			for (var i = maps.length - 1; i >= 0; i--) {
				maps[i].flyTo({
					center: teamCoords
				});
				if (teamMarkers !== null) {
					teamMarkers[i].togglePopup();
				}
			}
		}
	}

	function commitsToMap() {
		var map = new mapboxgl.Map({
			container: 'map',
			style: 'mapbox://styles/mapbox/streets-v11',
			center: [team_long, team_lat],
			projection: { name: 'globe' },
			boxZoom: true,
			scrollZoom: true,
			zoom: 3.75
		});
		//@ts-ignore
		maps.push(map);

		var coordsArray: [Number, Number][] = [];

		var count = 0;
		teamCommits.forEach(async (commit) => {
			count = count + 1;

			let coords = await commit.resultCoords;

			if (
				coordsArray.some(
					(playerCoord) => playerCoord[0] === coords[0] && playerCoord[1] === coords[1]
				)
			) {
				//console.log('Duplicate Location: ' + coords);
				coords[0] = coords[0] + count * 0.00075;
				coords[1] = coords[1] + count * 0.00075;
				//console.log('New Coords: ' + coords);
			}

			coordsArray.push(coords);
			const playerMarker = new mapboxgl.Marker({
				color: 'Red'
			})
				.setLngLat(coords)
				.setPopup(
					new mapboxgl.Popup().setHTML(
						'<div class=""><img class="h-24 mx-auto rounded-md flex" src=' +
							commit.playerImageUrl +
							'><h1 class="flex justify-center text-black">' +
							commit.name +
							'</h1><h1 class="flex justify-center text-black">' +
							commit.location +
							'</h1><h1 class="flex justify-center text-black">' +
							commit.stars +
							' ' +
							'(' +
							commit.score +
							')' +
							' ' +
							commit.position +
							'</h1></div>'
					)
				)
				.addTo(map);
			//@ts-ignore
			playerMarkers!.push({ marker: playerMarker, id: commit.name });
		});

		const teamMarker = new mapboxgl.Marker({
			color: 'black',
			offset: [0.05, 0.05],
			scale: 1.15
		})
			.setLngLat([team_long, team_lat])
			.setPopup(
				new mapboxgl.Popup().setHTML(
					'<img class="h-24" src=' +
						teamImageUrl +
						'><h1 class="flex justify-center text-black">' +
						selectedTeam +
						'</h1><h1 class="flex justify-center text-black">' +
						selectedTeamLocation +
						'</h1>'
				)
			)
			.addTo(map);
		teamMarkers!.push(teamMarker);
	}

	let selectedConference: string = 'B1G';
	let selectedTeam: string = 'Nebraska';
	let selectedTeamLocation: string = 'Lincoln, NE';
	let showTeamList = true;
	let showPlayerList = true;

	let loadingPlayers = false;
	let playersLoaded = false;
	let teamCommits: {
		name: string;
		playerImageUrl: string;
		position: string;
		score: string;
		stars: string;
		location: string;
		resultCoords: Promise<[number, number]>;
	}[] = [];
	let numCommits = 0;
	let teamImageUrl =
		'https://s3media.247sports.com/Uploads/Assets/814/84/11084814.png?fit=bounds&crop=50:50,offset-y0.50&width=50&height=50&fit=crop';

	let teamNameUrl = 'nebraska';
	let year = 2023;

	let url =
		'https://247sports.com/college/' +
		teamNameUrl +
		'/Season/' +
		year.toString() +
		'-Football/Commits/';

	async function changeYear() {
		loadingStatus = true;
		if (year < 2026 && year > 1998) {
			removeTeamMarkers();
			removeMaps();
			// removePlayerMarkers();
			url =
				'https://247sports.com/college/' +
				teamNameUrl +
				'/Season/' +
				year.toString() +
				'-Football/Commits/';
			playersLoaded = false;
			teamCommits = [];
			await getCommits();
			commitsToMap();
		}
		loadingStatus = false;
	}

	function selectConference(conference: string): void {
		if (loadingStatus) {
			return;
		}
		selectedConference = conference;
		conferenceToggleDropdown();
	}

	async function selectTeam(teamName: string): Promise<void> {
		loadingStatus = true;
		if (year > 2026 || year < 1999) {
			return;
		}
		removeTeamMarkers();
		//console.log(teamMarkers);
		removeMaps();

		teamToggleDropdown();
		selectedTeam = teamName;
		console.log('Selected Team:' + selectedTeam);
		teamNameUrl = selectedTeam.toLowerCase();
		teamNameUrl = teamNameUrl.split(' ').join('-');
		if (teamNameUrl.includes('&')) {
			// Texas A&M Adjustment
			teamNameUrl = teamNameUrl.replace('&', '');
			//console.log(teamNameUrl);
		}

		url =
			'https://247sports.com/college/' +
			teamNameUrl +
			'/Season/' +
			year.toString() +
			'-Football/Commits/';
		playersLoaded = false;
		teamCommits = [];

		await updateTeamLocation();
		console.log(selectedTeamLocation);
		await getCommits();
		commitsToMap();
		loadingStatus = false;
	}

	async function getCommits() {
		loadingPlayers = true;
		try {
			const htmlData = await axios.get(url);
			const $ = load(htmlData.data);
			let count = 0;

			teamImageUrl = $('.ir-bar__coach-block', htmlData.data).children('img').attr('src')!;

			$('.ri-page__list-item', htmlData.data).each((index, player1) => {
				if (player1.attribs.class === 'ri-page__list-item list-header') {
					return;
				}

				let playerImageUrl = $('.circle-image-block', player1)
					.children('img.jsonly')
					.attr('data-src')!;

				let name = $('.ri-page__name-link', player1).text();
				let location = $('.meta', player1).text();
				let position = $('.position', player1).text();
				let score = $('.score', player1).text();
				let stars = '';

				parseFloat(score) > 0.98832
					? (stars = '⭐⭐⭐⭐⭐')
					: parseFloat(score) > 0.89
					? (stars = '⭐⭐⭐⭐')
					: parseFloat(score) > 0.7999
					? (stars = '⭐⭐⭐')
					: score === 'NA'
					? (stars = 'Not Rated')
					: (stars = '⭐⭐');

				location = location.trim();

				count = count + 1;

				const resultCoords: Promise<[number, number]> = geoCodeLocations(location);

				let playerData = { name, playerImageUrl, position, score, stars, location, resultCoords };

				teamCommits.push(playerData);
			});

			numCommits = count;

			loadingPlayers = false;
			playersLoaded = true;
		} catch (err) {
			console.log(err);
		}
	}
</script>

<DarkMode bind:theme />

<link href="https://api.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.css" rel="stylesheet" />

<h1 class="flex mx-auto justify-center text-2xl lg:text-3xl font-bold">
	NCAA College Football Commit Map
</h1>

<div class=" flex flex-row justify-center ">
	<div
		on:mouseleave={() => {
			conferenceDropdownPopoverShow = false;
		}}
		class="relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg border-black border {conferenceDropdownPopoverShow
			? 'bg-slate-300'
			: ''}"
	>
		<button
			class=" font-bold uppercase text-sm md:px-6 px-3 py-3  rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
			type="button"
			bind:this={conferenceBtnDropdownRef}
			on:click={conferenceToggleDropdown}
			on:mouseover={conferenceToggleDropdown}
			on:focus={conferenceToggleDropdown}
		>
			{selectedConference}
		</button>
		<div
			bind:this={conferencePopoverDropdownRef}
			class="bg-white  text-base z-50 float-left  list-none text-left rounded shadow-lg   {conferenceDropdownPopoverShow
				? 'block'
				: 'hidden'}"
		>
			<ConferenceButton
				conference="ACC"
				{selectedConference}
				on:click={(e) => {
					selectConference('ACC');
				}}
			/>
			<ConferenceButton
				conference="AAC"
				{selectedConference}
				on:click={(e) => {
					selectConference('AAC');
				}}
			/>
			<ConferenceButton
				conference="CUSA"
				{selectedConference}
				on:click={(e) => {
					selectConference('CUSA');
				}}
			/>
			<ConferenceButton
				conference="B1G"
				{selectedConference}
				on:click={(e) => {
					selectConference('B1G');
				}}
			/>
			<ConferenceButton
				conference="Big-12"
				{selectedConference}
				on:click={(e) => {
					selectConference('Big-12');
				}}
			/>
			<ConferenceButton
				conference="Pac-12"
				{selectedConference}
				on:click={(e) => {
					selectConference('Pac-12');
				}}
			/>

			<ConferenceButton
				conference="SEC"
				{selectedConference}
				on:click={(e) => {
					selectConference('SEC');
				}}
			/>
			<ConferenceButton
				conference="MAC"
				{selectedConference}
				on:click={(e) => {
					selectConference('MAC');
				}}
			/>
			<ConferenceButton
				conference="MWEST"
				{selectedConference}
				on:click={(e) => {
					selectConference('MWEST');
				}}
			/>
			<ConferenceButton
				conference="SBC"
				{selectedConference}
				on:click={(e) => {
					selectConference('SBC');
				}}
			/>
			<ConferenceButton
				conference="IND"
				{selectedConference}
				on:click={(e) => {
					selectConference('IND');
				}}
			/>
		</div>
	</div>

	<div
		on:mouseleave={() => {
			teamDropdownPopoverShow = false;
		}}
		class="relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg border-black border {teamDropdownPopoverShow
			? 'bg-slate-300'
			: ''}"
	>
		<button
			class="font-bold uppercase text-sm md:px-6 px-3 py-3 rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
			type="button"
			bind:this={teamBtnDropdownRef}
			on:click={teamToggleDropdown}
			on:mouseover={teamToggleDropdown}
			on:focus={teamToggleDropdown}
		>
			{selectedTeam}
		</button>
		<div
			bind:this={teamPopoverDropdownRef}
			class="bg-white text-base  z-50 float-left  list-none text-left rounded shadow-lg mt-1 min-w-48 {teamDropdownPopoverShow
				? 'block'
				: 'hidden'}"
		>
			{#if showTeamList}
				{#if selectedConference === 'B1G'}
					{#each B1GArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'SEC'}
					{#each SECArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'Pac-12'}
					{#each Pac12Array as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'AAC'}
					{#each AACArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'CUSA'}
					{#each CUSAArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'ACC'}
					{#each ACCArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'Big-12'}
					{#each Big12Array as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}

						{:else if selectedConference === 'MAC'}
						{#each MACArray as team}
							<TeamListButton
								{selectedTeam}
								{team}
								on:click={(e) => {
									loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
								}}
							/>{/each}
					{:else if selectedConference === 'MWEST'}
						{#each MWESTArray as team}
							<TeamListButton
								{selectedTeam}
								{team}
								on:click={(e) => {
									loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
								}}
							/>{/each}
					{:else if selectedConference === 'SBC'}
						{#each SBCArray as team}
							<TeamListButton
								{selectedTeam}
								{team}
								on:click={(e) => {
									loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
								}}
							/>{/each}		
				{:else}
					{#each INDArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{/if}
			{/if}
		</div>
	</div>

	<div class="row-auto flex my-2 md:mx-1 justify-center rounded-md border-black border max-w-fit ">
		<h1 class=" font-semibold  my-auto mx-1" for="yearInput">Year:</h1>
		<input
			class=" text-black font-semibold text-center my-2 mr-1 border rounded-md"
			name="yearInput"
			type="number"
			onKeyDown={(e) => {
				false;
			}}
			min="1999"
			max="2026"
			bind:value={year}
		/>
		<button
			class="border hover:bg-gray-300 border-black p-1 my-2 rounded-md mr-1 font-semibold"
			on:click={() => {
				loadingStatus ? '' : changeYear();
			}}>GO</button
		>
	</div>
	<div
		on:mouseleave={() => {
			helpDropdownPopoverShow = false;
		}}
		class="relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg border-black border {helpDropdownPopoverShow
			? 'bg-slate-300'
			: ''}"
	>
		<button
			class="font-bold uppercase text-sm md:px-6 px-3 py-3 rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
			type="button"
			bind:this={helpBtnDropdownRef}
			on:click={helpToggleDropdown}
			on:mouseover={helpToggleDropdown}
			on:focus={helpToggleDropdown}
		>
			?
		</button>
		<div
			bind:this={helpPopoverDropdownRef}
			class="bg-white text-base z-50 float-right  list-none text-left rounded shadow-lg mt-1 min-w-48 {helpDropdownPopoverShow
				? 'block'
				: 'hidden'}"
		>
			{#if helpDropdownPopoverShow}
				<div class="flex-col w-36 border-black border-2 rounded-md">
					<p class="mb-2 mx-2 text-black">
						<span class="font-semibold text-black">Black Marker: </span> Team Location
					</p>
					<p class="mb-2 mx-2 text-black">
						<span class="font-semibold text-red-500">Red Marker: </span>Commit Location
					</p>
					<p class=" flex mb-2 mx-2 text-black">
						To change year, use arrows or type year and press the GO button
					</p>
				</div>
			{/if}
		</div>
	</div>
</div>

<div
	class="lg:grid lg:grid-cols-5 lg:grid-rows-1 lg:max-w-screen-2xl lg:mx-auto lg:border lg:w-full  lg:h-fit lg:mb-2 "
>
	<div class="lg:grid lg:col-span-4 lg:order-last ">
		<div
			class="container  mx-auto lg:max-h-[38rem]  lg:max-w-7xl lg:min-w-2xl  aspect-video bg-black rounded-md "
			id="map"
		/>
		<div class=" mx-auto md:w-11/12 lg:w-full max-w-7xl m-1 justify-center flex ">
			<button
				class="border border-black rounded-lg p-1  hover:bg-gray-300"
				type="menu"
				on:click={() => (theme === 'light' ? (theme = 'dark') : (theme = 'light'))}
			>
				{theme === 'light' ? 'Dark Mode' : 'Light Mode'}
			</button>
			<button
				class="border border-black rounded-lg p-1  hover:bg-gray-300 lg:hidden"
				on:click={() => {
					loadingStatus ? '' : (showPlayerList = !showPlayerList);
				}}>{showPlayerList ? 'Hide Player List' : 'Show Player List'}</button
			>
			<button
				class="border border-black rounded-lg p-1  hover:bg-gray-300"
				on:click={() => {
					loadingStatus ? '' : teamCenter([team_long, team_lat]);
				}}>Center on Selected Team</button
			>
		</div>
	</div>

	<div class="w-11/12 justify-center mx-auto grid grid-cols-2 lg:grid-cols-1  lg:max-h-[39rem]">
		{#if loadingPlayers && showPlayerList}
			<div>Loading Players</div>
		{/if}
		{#if teamCommits.length == 0 && playersLoaded && showPlayerList}
			<h1 class="justify-center mx-auto font-semibold  text-xl col-span-2 lg:col-span-1">
				{playersLoaded && numCommits == 0 ? 'Zero Commits' : ''}
			</h1>
		{/if}
		{#if teamCommits.length > 0 && playersLoaded && showPlayerList}
			<h1 class="justify-center mx-auto font-semibold  text-xl col-span-2 lg:col-span-1">
				{numCommits != 0 ? 'Commits (' + numCommits + ')' : ''}
			</h1>

			<div class=" lg:overflow-y-auto lg:grid-cols-1 lg:col-span-1  grid grid-cols-2 col-span-2">
				{#each teamCommits as commit}
					<div
						class="border hover:bg-red-400 hover:scale-105 hover:cursor-pointer lg:hover:scale-100 rounded-md border-black grid lg:col-span-1"
						on:click={async () => {
							var coords = await commit.resultCoords;

							commitCenter(coords, commit.name);
						}}
					>
						<h1 class="px-1 pt-1 md:p-1 font-semibold">{commit.name}</h1>
						<h1 class="px-1 pt-1 md:p-1 ">{commit.stars} {commit.position}</h1>
						<h1 class="px-1 md:p-1">{commit.location}</h1>
					</div>
				{/each}
			</div>
		{/if}
	</div>
</div>
<div class="border max-w-11-12 min-w-max h-10 mx-auto sticky flex justify-center">
	<h1 class="justify-center flex flex-auto p-1">
		Made by <a
			class="ml-1 px-1  border underline rounded-lg"
			href="https://kyleolson.vercel.app/"
			target="_blank"
		>
			Kyle Olson</a
		>
	</h1>
</div>

<style>
	:global(.dark) {
		background: #032f62;
		color: #f1f8ff;
	}
</style>
