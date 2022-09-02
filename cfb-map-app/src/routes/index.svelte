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
	import YearButton from '../components/YearButton.svelte';

	let theme: any;
	afterUpdate(() => {
		document.body.className = theme; // "dark" or "light"
	});

	let loadingStatus = false;

	let conferenceDropdownPopoverShow = false;
	let conferenceBtnDropdownRef: Element | VirtualElement;
	let conferencePopoverDropdownRef: HTMLElement;

	let badUrl = '';
	let classRanking = '';

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

	let yearDropdownPopoverShow = false;
	let yearBtnDropdownRef: Element | VirtualElement;
	let yearPopoverDropdownRef: HTMLElement;

	const yearToggleDropdown = () => {
		if (yearDropdownPopoverShow) {
			yearDropdownPopoverShow = false;
		} else {
			yearDropdownPopoverShow = true;
			createPopper(yearBtnDropdownRef, yearPopoverDropdownRef, {
				placement: 'bottom-start'
			});
		}
	};

	type teamInfo = { name: string; slug: string; location: string; color?: string };

	let B1GArray: teamInfo[] = [
		{ name: 'Illinois', slug: 'illinois', location: 'Champaign, IL', color: '#E84A27' },
		{ name: 'Iowa', slug: 'iowa', location: 'Iowa City, IA', color: '#FFCD00' },
		{ name: 'Maryland', slug: 'maryland', location: 'College Park, MD', color: '#E03A3E' },
		{ name: 'Indiana', slug: 'indiana', location: 'Bloomington, IN', color: '#990000' },
		{
			name: 'Michigan State',
			slug: 'michigan-state',
			location: 'East Lansing, MI',
			color: '#18453B'
		},
		{ name: 'Michigan', slug: 'michigan', location: 'Ann Arbor, MI', color: '#00274C' },
		{ name: 'Minnesota', slug: 'minnesota', location: 'Minneapolis, MN', color: '#7A0019' },
		{ name: 'Nebraska', slug: 'nebraska', location: 'Lincoln, NE', color: '#E41C38' },
		{ name: 'Northwestern', slug: 'northwestern', location: 'Evanston, IL', color: '#4E2A84' },
		{ name: 'Ohio State', slug: 'ohio-state', location: 'Columbus, OH', color: '#BB0000' },
		{ name: 'Penn State', slug: 'penn-state', location: 'State College, PA', color: '#041E42' },
		{ name: 'Purdue', slug: 'purdue', location: 'West Lafayette, IN', color: '#CEB888' },
		{ name: 'Rutgers', slug: 'rutgers', location: 'New Brunswick, NJ', color: '#CC0033' },
		{ name: 'Wisconsin', slug: 'wisconsin', location: 'Madison, WI', color: '#C5050C' }
	];

	let ACCArray: teamInfo[] = [
		{
			name: 'Boston College',
			slug: 'boston-college',
			location: 'Chestnut Hull, MA',
			color: '#98002E'
		},
		{ name: 'Clemson', slug: 'clemson', location: 'Clemson, SC', color: '#F56600' },
		{ name: 'Duke', slug: 'duke', location: 'Durham, NC', color: '#003087' },
		{ name: 'Florida State', slug: 'florida-state', location: 'Tallahassee, FL', color: '#782F40' },
		{ name: 'Georgia Tech', slug: 'georgia-tech', location: 'Atlanta, GA', color: '#B3A369' },
		{ name: 'Louisville', slug: 'louisville', location: 'Louisville, KY', color: '#AD0000' },
		{ name: 'Miami', slug: 'miami', location: 'Coral Gables, FL', color: '#F47321' },
		{ name: 'NC State', slug: 'nc-state', location: 'Raleigh, NC', color: '#CC0000' },
		{
			name: 'North Carolina',
			slug: 'north-carolina',
			location: 'Chapel Hill, NC',
			color: '#7BAFD4'
		},
		{ name: 'Syracuse', slug: 'syracuse', location: 'Syracuse, NY', color: '#F76900' },
		{ name: 'Pittsburgh', slug: 'pittsburgh', location: 'Pittsburgh, PA', color: '#003594' },
		{ name: 'Virginia', slug: 'virginia', location: 'Charlottesville, VA', color: '#F84C1E' },
		{ name: 'Wake Forest', slug: 'wake-forest', location: 'Winston-Salem, NC', color: '#9E7E38' },
		{ name: 'Virginia Tech', slug: 'virgina-tech', location: 'Blacksburg, VA', color: '#630031' }
	];

	let SECArray: teamInfo[] = [
		{ name: 'Alabama', slug: 'alabama', location: 'Tuscaloosa, AL', color: '#9E1B32' },
		{ name: 'Texas A&M', slug: 'texas-am', location: 'College Station, TX', color: '#500000' },
		{ name: 'Arkansas', slug: 'arkansas', location: 'Fayetteville, AR', color: '#9D2235' },
		{ name: 'Missouri', slug: 'missouri', location: 'Columbia, MO', color: '#F1B82D' },
		{ name: 'Kentucky', slug: 'kentucky', location: 'Lexington, KY', color: '#0033A0' },
		{ name: 'Vanderbilt', slug: 'vanderbilt', location: 'Nashville, TN', color: '#866D4B' },
		{ name: 'Tennessee', slug: 'tennessee', location: 'Knoxville, TN', color: '#FF8200' },
		{ name: 'Georgia', slug: 'georgia', location: 'Athens, GA', color: '#BA0C2F' },
		{ name: 'Florida', slug: 'florida', location: 'Gainesville, FL', color: '#0021A5' },
		{
			name: 'Mississippi State',
			slug: 'mississippi-state',
			location: 'Starkville, MS',
			color: '#660000'
		},
		{ name: 'Ole Miss', slug: 'ole-miss', location: 'Oxford, MS', color: '#006BA6' },
		{ name: 'South Carolina', slug: 'south-carolina', location: 'Columbia, SC', color: '#73000A' },
		{ name: 'Auburn', slug: 'auburn', location: 'Auburn, AL', color: '#0C2340' },
		{ name: 'LSU', slug: 'lsu', location: 'Baton Rouge, LA', color: '#461D7C' }
	];

	let Big12Array: teamInfo[] = [
		{ name: 'Baylor', slug: 'baylor', location: 'Waco, TX', color: '#154734' },
		{ name: 'Iowa State', slug: 'iowa-state', location: 'Ames, IA', color: '#C8102E' },
		{ name: 'Kansas', slug: 'kansas', location: 'Lawrence, KS', color: '#0051BA' },
		{ name: 'Kansas State', slug: 'kansas-state', location: 'Manhattan, KS', color: '#512888' },
		{ name: 'Oklahoma', slug: 'oklahoma', location: 'Norman, OK', color: '#841617' },
		{
			name: 'Oklahoma State',
			slug: 'oklahoma-state',
			location: 'Stillwater, OK',
			color: '#FF7300'
		},
		{ name: 'TCU', slug: 'tcu', location: 'Forth Worth, TX', color: '#4D1979' },
		{ name: 'Texas', slug: 'texas', location: 'Austin, TX', color: '#BF5700' },
		{ name: 'Texas Tech', slug: 'texas-tech', location: 'Lubbock, TX', color: '#CC0000' },
		{ name: 'West Virginia', slug: 'west-virginia', location: 'Morgantown, WV', color: '#002855' }
	];

	let Pac12Array: teamInfo[] = [
		{ name: 'Colorado', slug: 'colorado', location: 'Boulder, CO', color: '#CFB87C' },
		{ name: 'Utah', slug: 'utah', location: 'Salt Lake City, UT', color: '#CC0000' },
		{
			name: 'Washington State',
			slug: 'washington-state',
			location: 'Pullman, WA',
			color: '#981E32'
		},
		{ name: 'Washington', slug: 'washington', location: 'Seattle, WA', color: '#4B2E83' },
		{ name: 'Oregon', slug: 'oregon', location: 'Eugene, OR', color: '#154733' },
		{ name: 'Oregon State', slug: 'oregon-state', location: 'Corvallis, OR', color: '#DC4405' },
		{ name: 'California', slug: 'california', location: 'Berkeley, CA', color: '#003262' },
		{ name: 'UCLA', slug: 'ucla', location: 'Beverly Hills, CA', color: '#2D68C4' },
		{ name: 'USC', slug: 'usc', location: 'Los Angeles, CA', color: '#990000' },
		{ name: 'Arizona State', slug: 'arizona-state', location: 'Tempe, AZ', color: '#8C1D40' },
		{ name: 'Arizona', slug: 'arizona', location: 'Tucson, AZ', color: '#003366' },
		{ name: 'Stanford', slug: 'stanford', location: 'Stanford, CA', color: '#8C1515' }
	];

	let AACArray: teamInfo[] = [
		{ name: 'Cincinnati', slug: 'cincinnati', location: 'Cincinnati, OH', color: '#E00122' },
		{ name: 'East Carolina', slug: 'east-carolina', location: 'Greenville, NC', color: '#592A8A' },
		{ name: 'Houston', slug: 'houston', location: 'Houston, TX', color: '#C8102E' },
		{ name: 'Memphis', slug: 'memphis', location: 'memphis, TN', color: '#003087' },
		{ name: 'Navy', slug: 'navy', location: 'Annapolis, MD', color: '#00205B' },
		{ name: 'SMU', slug: 'smu', location: 'Dallas, TX', color: '#C8102E' },
		{ name: 'South Florida', slug: 'south-florida', location: 'Tampa, FL', color: '#006747' },
		{ name: 'Temple', slug: 'temple', location: 'Philadelphia, PA', color: '#9D2235' },
		{ name: 'Tulane', slug: 'tulane', location: 'New Orleans, LA', color: '#006747' },
		{ name: 'Tulsa', slug: 'tulsa', location: 'Tulsa, OK', color: '#002D72' },
		{ name: 'UCF', slug: 'central-florida', location: 'Orlando, FL', color: '#BA9B37' }
	];

	let INDArray: teamInfo[] = [
		{ name: 'Army', slug: 'army', location: 'West Point, NY', color: '#D4BF91' },
		{ name: 'BYU', slug: 'byu', location: 'Provo, UT', color: '#002E5D' },
		{ name: 'Notre Dame', slug: 'notre-dame', location: 'South Bend, IN', color: '#0C2340' }
	];

	let CUSAArray: teamInfo[] = [
		{ name: 'Charlotte', slug: 'charlotte', location: 'Charlotte, NC', color: '#046A38' },
		{
			name: 'Florida International',
			slug: 'florida-international',
			location: 'Miami, FL',
			color: '#081E3F'
		},
		{
			name: 'Florida Atlantic',
			slug: 'florida-atlantic',
			location: 'Boca Raton, FL',
			color: '#003366'
		},
		{ name: 'Louisiana Tech', slug: 'louisiana-tech', location: 'Ruston, LA', color: '#002F8B' },
		{
			name: 'Middle Tennessee State',
			slug: 'middle-tennessee-state',
			location: 'Murfreesboro, TN',
			color: '#0066CC'
		},
		{ name: 'North Texas', slug: 'north-texas', location: 'Denton, TX', color: '#00853E' },
		{ name: 'Rice', slug: 'rice', location: 'Houston, TX', color: '#00205B' },
		{
			name: 'Alabama Birmingham',
			slug: 'alabama-birmingham',
			location: 'Birmingham, AL',
			color: '#006341'
		},
		{ name: 'UTEP', slug: 'utep', location: 'El Paso, TX', color: '#FF8200' },
		{
			name: 'Western Kentucky',
			slug: 'western-kentucky',
			location: 'Bowling Green, KY',
			color: '#C60C30'
		},
		{ name: 'UTSA', slug: 'utsa', location: 'San Antonio, TX', color: '#F15A22' }
	];

	let MACArray: teamInfo[] = [
		{ name: 'Akron', slug: 'akron', location: 'Akron, OH', color: '#041E42' },
		{ name: 'Ball State', slug: 'ball-state', location: 'Muncie, IN', color: '#BA0C2F' },
		{
			name: 'Bowling Green',
			slug: 'bowling-green',
			location: 'Bowling Green, OH',
			color: '#FE5000'
		},
		{ name: 'Buffalo', slug: 'buffalo', location: 'Buffalo, NY', color: '#005BBB' },
		{
			name: 'Central Michigan',
			slug: 'Central Michigan',
			location: 'Mount Pleasant, MI',
			color: '#6A0032'
		},
		{
			name: 'Eastern Michigan',
			slug: 'eastern-michigan',
			location: 'Ypsilanti, MI',
			color: '#006633'
		},
		{ name: 'Kent State', slug: 'kent-state', location: 'Kent, OH', color: '#002664' },
		{ name: 'Miami Ohio', slug: 'miami-ohio', location: 'Oxford, Ohio', color: '#B61E2E' },
		{
			name: 'Northern Illinois',
			slug: 'northern-illinois',
			location: 'Dekalb, IL',
			color: '#BA0C2F'
		},
		{ name: 'Ohio', slug: 'ohio', location: 'Athens, OH', color: '#00694E' },
		{ name: 'Toledo', slug: 'toldeo', location: 'Toledo, OH', color: '#15397F' },
		{
			name: 'Western Michigan',
			slug: 'western-michigan',
			location: 'Kalamazoo, MI',
			color: '#6C4023'
		}
	];

	let MWESTArray: teamInfo[] = [
		{ name: 'Air Force', slug: 'air-force', location: 'Colorado Springs, CO', color: '#003087' },
		{ name: 'Boise State', slug: 'boise-state', location: 'Boise, ID', color: '#0033A0' },
		{
			name: 'Colorado State',
			slug: 'colorado-state',
			location: 'Fort Collins, CO',
			color: '#1E4D2B'
		},
		{ name: 'Fresno State', slug: 'fresno-state', location: 'Fresno, CA', color: '#DB0032' },
		{ name: 'Hawaii', slug: 'hawaii', location: 'Honolulu, HI', color: '#024731' },
		{ name: 'Nevada', slug: 'nevada', location: 'Reno, NV', color: '#003366' },
		{ name: 'New Mexico', slug: 'New Mexico', location: 'Albuquerque, NM', color: '#BA0C2F' },
		{
			name: 'San Diego State',
			slug: 'san-diego-state',
			location: 'San Diego, CA',
			color: '#A6192E'
		},
		{ name: 'San Jose State', slug: 'san-jose-state', location: 'San Jose, CA', color: '#0055A2' },
		{ name: 'UNLV', slug: 'unlv', location: 'Las Vegas, NV', color: '#CF0A2C' },
		{ name: 'Utah State', slug: 'utah-state', location: 'Logan, UT', color: '#00263A' },
		{ name: 'Wyoming', slug: 'wyoming', location: 'Laramie, WY', color: '#492F24' }
	];

	let SBCArray: teamInfo[] = [
		{
			name: 'Appalachian State',
			slug: 'appalachian-state',
			location: 'Boone, NC',
			color: '#FFCC00'
		},
		{ name: 'Arkansas State', slug: 'arkansas-state', location: 'Jonesboro, AR', color: '#CC092F' },
		{
			name: 'Coastal Carolina',
			slug: 'coastal-carolina',
			location: 'Conway, SC',
			color: '#006F71'
		},
		{
			name: 'Georgia Southern',
			slug: 'georgia-southern',
			location: 'Statesboro, GA',
			color: '#011E41'
		},
		{ name: 'Georgia State', slug: 'georgia-state', location: 'Atlanta, GA', color: '#0039A6' },
		{
			name: 'James Madison',
			slug: 'james-madison',
			location: 'Harrisonburg, VA',
			color: '#450084'
		},
		{ name: 'Louisiana-Monroe', slug: 'louisian-monroe', location: 'Monroe, LA', color: '#840029' },
		{ name: 'Louisiana', slug: 'louisiana', location: 'Lafayette, LA', color: '#CE181E' },
		{ name: 'Marshall', slug: 'marshall', location: 'Hungtington, WV', color: '#00B140' },
		{ name: 'Old Dominion', slug: 'old-dominion', location: 'Norfolk, VA', color: '#003057' },
		{ name: 'South Alabama', slug: 'south-alabama', location: 'Mobile, AL', color: '#00205B' },
		{ name: 'Southern Miss', slug: 'southern-miss', location: 'Hattiesburg, MS', color: '#FFAB00' },
		{ name: 'Texas State', slug: 'texas-state', location: 'San Marcos, TX', color: '#501214' },
		{ name: 'Troy', slug: 'troy', location: 'Troy, AL', color: '#8A2432' }
	];

	let YearsArray: number[] = [
		2025, 2024, 2023, 2022, 2021, 2020, 2019, 2018, 2017, 2016, 2015, 2014, 2013, 2012, 2011, 2010,
		2009, 2008, 2007, 2006, 2005, 2004, 2003, 2002, 2001, 2000, 1999
	];

	let selectedConference: string = 'B1G';
	let selectedTeam: string = 'Nebraska';

	let selectedTeamLocation: string = 'Lincoln, NE';
	let selectedColor: string = '#E41C38';
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
	let selectedYear = 2022;

	let url =
		'https://247sports.com/college/' +
		teamNameUrl +
		'/Season/' +
		selectedYear.toString() +
		'-Football/Commits/';
	mapboxgl.accessToken = import.meta.env.VITE_MAPBOX_ACCESS_TOKEN;

	let [team_long, team_lat] = [-96.681679, 40.806]; //Lincoln, NE

	async function updateTeamLocation() {
		if (selectedConference === 'B1G') {
			B1GArray.forEach(async (team) => {
				if (team.name === selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'SEC') {
			SECArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'ACC') {
			ACCArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'CUSA') {
			CUSAArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'AAC') {
			AACArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'Big-12') {
			Big12Array.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'Pac-12') {
			Pac12Array.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'SBC') {
			SBCArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'MAC') {
			MACArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else if (selectedConference === 'MWEST') {
			MWESTArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
					[team_long, team_lat] = await geoCodeLocations(selectedTeamLocation);
				}
			});
		} else {
			INDArray.forEach(async (team) => {
				if (team.name == selectedTeam) {
					selectedTeamLocation = team.location;
					selectedColor = team.color!;
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
		await getCommits('year');
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
				color: selectedColor
			})
				.setLngLat(coords)
				.setPopup(
					new mapboxgl.Popup().setHTML(
						'<div class=""><img class="h-24 mx-auto rounded-md flex"  src=' +
							commit.playerImageUrl +
							' alt="No Logo From 247"><h1 class="flex justify-center text-black">' +
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
			color: 'white',
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

	async function changeYear(year: number) {
		loadingStatus = true;
		badUrl = '';

		if (year < 2026 && year > 1998) {
			removeTeamMarkers();
			removeMaps();
			// removePlayerMarkers();
			selectedYear = year;
			url =
				'https://247sports.com/college/' +
				teamNameUrl +
				'/Season/' +
				year.toString() +
				'-Football/Commits/';
			playersLoaded = false;
			teamCommits = [];
			await getCommits('year');

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
		if (selectedYear > 2026 || selectedYear < 1999) {
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
			selectedYear.toString() +
			'-Football/Commits/';
		playersLoaded = false;
		teamCommits = [];

		await updateTeamLocation();
		console.log(selectedTeamLocation);
		await getCommits('team');

		commitsToMap();

		loadingStatus = false;
	}

	async function getCommits(change: string) {
		loadingPlayers = true;
		try {
			const htmlData = await axios.get(url);
			const $ = load(htmlData.data);
			let count = 0;
			change === 'team' || teamImageUrl === ''
				? (teamImageUrl = $('.ir-bar__coach-block', htmlData.data).children('img').attr('src')!)
				: '';
			if (typeof teamImageUrl == 'undefined') {
				teamImageUrl = '';
			}
			console.log('teamImageUrl:' + teamImageUrl);

			let blankCheck = $('.ri-page__list-item--no-results', htmlData.data).text().toString();
			if (blankCheck.includes('No Results')) {
				console.log('blank Check:' + blankCheck);
				badUrl = url;
				numCommits = 0;
				loadingPlayers = false;
				playersLoaded = true;
				classRanking = 'NA';
				return;
			}

			classRanking = $('.ir-bar__number', htmlData.data).first().text();
			console.log(classRanking);

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
			// }
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
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 border-2 rounded-lg ${
			conferenceDropdownPopoverShow ? 'bg-slate-400' : ''
		}`}
		style="border-color: {selectedColor};"
	>
		<button
			class=" font-bold uppercase text-sm md:px-6 px-3 py-3  rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
			type="button"
			bind:this={conferenceBtnDropdownRef}
			on:click={conferenceToggleDropdown}
			on:mouseover={conferenceToggleDropdown}
			on:focus={conferenceToggleDropdown}
		>
			{selectedConference} ▼
		</button>
		<div
			bind:this={conferencePopoverDropdownRef}
			class=" text-base z-50 float-left list-none text-left rounded shadow-lg  {conferenceDropdownPopoverShow
				? 'block'
				: 'hidden'}"
		>
			<ConferenceButton
				conference="ACC"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('ACC');
				}}
			/>
			<ConferenceButton
				conference="AAC"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('AAC');
				}}
			/>
			<ConferenceButton
				conference="CUSA"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('CUSA');
				}}
			/>
			<ConferenceButton
				conference="B1G"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('B1G');
				}}
			/>
			<ConferenceButton
				conference="Big-12"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('Big-12');
				}}
			/>
			<ConferenceButton
				conference="Pac-12"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('Pac-12');
				}}
			/>

			<ConferenceButton
				conference="SEC"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('SEC');
				}}
			/>
			<ConferenceButton
				conference="MAC"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('MAC');
				}}
			/>
			<ConferenceButton
				conference="MWEST"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('MWEST');
				}}
			/>
			<ConferenceButton
				conference="SBC"
				{selectedConference}
				{selectedColor}
				on:click={(e) => {
					selectConference('SBC');
				}}
			/>
			<ConferenceButton
				conference="IND"
				{selectedConference}
				{selectedColor}
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
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg border-2  ${
			teamDropdownPopoverShow ? 'bg-slate-400' : ''
		}`}
		style="border-color: {selectedColor}"
	>
		<button
			class={`font-bold uppercase text-sm md:px-6 px-3 py-3 rounded cursor-default `}
			type="button"
			bind:this={teamBtnDropdownRef}
			on:click={teamToggleDropdown}
			on:mouseover={teamToggleDropdown}
			on:focus={teamToggleDropdown}
		>
			{selectedTeam} ▼
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
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'SEC'}
					{#each SECArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'Pac-12'}
					{#each Pac12Array as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'AAC'}
					{#each AACArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'CUSA'}
					{#each CUSAArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'ACC'}
					{#each ACCArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'Big-12'}
					{#each Big12Array as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'MAC'}
					{#each MACArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'MWEST'}
					{#each MWESTArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else if selectedConference === 'SBC'}
					{#each SBCArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{:else}
					{#each INDArray as team}
						<TeamListButton
							{selectedTeam}
							{team}
							{selectedColor}
							on:click={(e) => {
								loadingStatus ? '' : team.name !== selectedTeam ? selectTeam(team.name) : '';
							}}
						/>{/each}
				{/if}
			{/if}
		</div>
	</div>

	<div
		on:mouseleave={() => {
			yearDropdownPopoverShow = false;
		}}
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg  border-2 ${
			yearDropdownPopoverShow ? 'bg-slate-400' : ''
		}`}
		style="border-color: {selectedColor}"
	>
		<button
			class="font-bold uppercase text-sm md:px-6 px-3 py-3 rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
			type="button"
			bind:this={yearBtnDropdownRef}
			on:click={yearToggleDropdown}
			on:mouseover={yearToggleDropdown}
			on:focus={yearToggleDropdown}
		>
			{selectedYear} ▼
		</button>
		<div
			bind:this={yearPopoverDropdownRef}
			class=" text-base bg-white  z-50 float-left  list-none text-left rounded shadow-lg mt-1 min-w-48 {yearDropdownPopoverShow
				? 'block'
				: 'hidden'}"
		>
			<ul class="overflow-y-auto h-[25rem]   bg-" id="scrollable">
				{#each YearsArray as year}
					<YearButton
						{selectedYear}
						{year}
						{selectedColor}
						on:click={(e) => {
							loadingStatus ? '' : year !== selectedYear ? changeYear(year) : '';
						}}
					/>{/each}
			</ul>
		</div>
	</div>

	<div
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg  border-2 `}
		style="border-color: {selectedColor}"
	>
		<h1
			class="font-bold uppercase text-sm md:px-6 px-3 py-3 rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none
	"
		>
			Rank: {classRanking}
		</h1>
	</div>

	<div
		on:mouseleave={() => {
			helpDropdownPopoverShow = false;
		}}
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg  border-2 ${
			helpDropdownPopoverShow ? 'bg-slate-400' : ''
		}`}
		style="border-color: {selectedColor}"
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
						<span class="font-semibold text-black">White Marker: </span> Team Location
					</p>
					<p class="mb-2 mx-2 text-black">
						<span class="font-semibold" style="color: {selectedColor}">Color Marker: </span>Commit
						Location
					</p>
					<p class="mb-2 mx-2 text-black">Ranking from 247 Overall</p>
				</div>
			{/if}
		</div>
	</div>
	<button
		class={`relative inline-flex align-middle w-fit md:mx-1 my-2 rounded-lg  border-2 
					hover:bg-slate-400
					`}
		style="border-color: {selectedColor}"
		on:click={() => (theme === 'light' ? (theme = 'dark') : (theme = 'light'))}
		><div
			class="font-bold uppercase text-sm md:px-4 px-1 py-1 my-auto rounded cursor-default shadow hover:shadow-lg outline-none focus:outline-none"
		>
			{#if theme === 'light'}
				<img class="aspect-square h-8" src="/dark.png" alt="Dark Mode" />
			{:else}
				<img class="aspect-square h-8" src="/light.png" alt="Light Mode" />
			{/if}
		</div>
	</button>
</div>

<div
	class="lg:grid lg:grid-cols-5 lg:grid-rows-1 lg:max-w-screen-2xl lg:mx-auto lg:w-full lg:h-fit lg:mb-2 "
>
	<div class="lg:grid lg:col-span-4 lg:order-last ">
		{#if loadingPlayers && showPlayerList}
			<div
				class="container mx-auto lg:max-h-[38rem] lg:max-w-7xl lg:min-w-2xl aspect-video bg-black rounded-md "
			>
				<iframe
					title="Loading Gif"
					src="https://giphy.com/embed/4MdAEEsl7L7j1ENX1I"
					class="giphy-embed justify-center mx-auto flex h-full aspect-video"
				/>
				<p>
					<a href="https://giphy.com/gifs/lights-off-lightsoff-lightsoffonline-4MdAEEsl7L7j1ENX1I"
						>via GIPHY</a
					>
				</p>
			</div>
		{:else}
			<div
				class="container mx-auto lg:max-h-[38rem] lg:max-w-7xl lg:min-w-2xl aspect-video bg-black rounded-md "
				id="map"
			/>
		{/if}

		<div class="mx-auto md:w-11/12 lg:w-full max-w-7xl m-1 flex  space-x-1 justify-center ">
			<button
				class={`rounded-lg p-1 hover:bg-gray-400 lg:hidden  border-2  max-h-10`}
				style="border-color: {selectedColor}"
				on:click={() => {
					loadingStatus ? '' : (showPlayerList = !showPlayerList);
				}}>{showPlayerList ? 'Hide Player List' : 'Show Player List'}</button
			>
			<button
				class={` border-2 rounded-lg p-1 hover:bg-gray-400  max-h-10 font-medium `}
				style="border-color: {selectedColor}"
				on:click={() => {
					loadingStatus ? '' : teamCenter([team_long, team_lat]);
				}}>Center on Selected Team</button
			>
		</div>
	</div>

	<div class="w-11/12 justify-center mx-auto grid grid-cols-2 lg:grid-cols-1 lg:max-h-[39rem]">
		{#if loadingPlayers && showPlayerList}
			<h1>Loading Players</h1>
		{/if}
		{#if teamCommits.length == 0 && playersLoaded && showPlayerList}
			<h1 class="justify-center mx-auto font-semibold  text-xl col-span-2 lg:col-span-1 my-0">
				{playersLoaded && numCommits == 0 ? 'Zero Commits' : ''}
			</h1>
			{#if badUrl !== ''}
				<a
					href={badUrl}
					target="_blank"
					class="border border-black rounded-lg p-1 underline h-fit  font-bold mx-auto hover:bg-gray-400 col-span-2 lg:col-span-1"
				>
					247 Sports Link
				</a>
			{/if}
		{/if}
		{#if teamCommits.length > 0 && playersLoaded && showPlayerList}
			<h1 class={`justify-center mx-auto font-semibold  text-2xl col-span-2 lg:col-span-1 `}>
				{numCommits != 0 ? 'Commits (' + numCommits + ')' : ''}
			</h1>

			<div
				class={`${
					theme === 'dark' ? 'bg-slate-600' : ''
				} rounded-md lg:overflow-y-auto lg:grid-cols-1 lg:col-span-1  grid grid-cols-2 col-span-2`}
			>
				{#each teamCommits as commit}
					<div
						class={` hover:border-4 hover:cursor-pointer rounded-md grid lg:col-span-1`}
						style="border-color: {selectedColor}"
						on:click={async () => {
							var coords = await commit.resultCoords;
							commitCenter(coords, commit.name);
						}}
					>
						<h1 class="px-1 pt-1.5 md:px-1 font-semibold" style="color: {selectedColor}">
							{commit.name}
						</h1>
						<h1 class="px-1 pt-.5 md:px-1 ">{commit.stars} {commit.position}</h1>
						<h1 class="pb-1.5 px-1">{commit.location}</h1>
					</div>
				{/each}
			</div>
		{/if}
	</div>
</div>

<div class="border max-w-11-12 min-w-max h-14 mx-auto m-5 sticky grid grid-cols-1 justify-center">
	<h1 class="justify-center flex flex-auto  h-fit">
		Made by <a
			class="ml-1 px-1  underline rounded-lg"
			href="https://kyleolson.vercel.app/"
			target="_blank"
		>
			Kyle Olson
		</a>
	</h1>
	<a class="justify-center flex underline" target="_blank" href="https://www.buymeacoffee.com/kyleolson"
		>☕️ Buy Me a Coffee
	</a>
</div>

<style>
	:global(.dark) {
		background: #222222;
		color: #f1f8ff;
	}
</style>
