<template>
	<div>
		<h1>User Location</h1>
		<div v-if="error" class="error__messages">
			<p>{{ error }}</p>
		</div>
		<div class="container">
			<button
				class="btn btn-primary"
				:disabled="isFetchingLocation"
				@click="getLocation"
			>
				Get Location
			</button>
		</div>
		<div class="location" v-if="address.full">
			<p>Full Address: {{ address.full }}</p>
			<p>latitude :{{ location.lat }}</p>
			<p>Longitude: {{ location.lng }}</p>
			<p>City :{{ address.city }} & State : {{ address.state }}</p>
		</div>

		<div class="loader" v-show="isLoading">
			<Spinner />
		</div>

		<section id="map" :class="{ loading: isLoading }"></section>
	</div>
</template>

<script>
import axios from 'axios';
import Spinner from '@/components/Spinner';

export default {
	name: 'UserLocation',
	components: {
		Spinner,
	},

	data() {
		return {
			isLoading: false,
			isFetchingLocation: false,
			location: {
				lat: 40.6892,
				lng: -74.0445,
				// default location is Liberty Island, NY
			},
			address: {
				city: '',
				state: '',
				full: '',
			},
			error: '',
		};
	},

	mounted() {
		this.showLocationOnMap();
	},

	methods: {
		getLocation() {
			this.isLoading = true;
			this.isFetchingLocation = true;
			if (navigator.geolocation) {
				navigator.geolocation.getCurrentPosition(
					(position) => {
						this.isFetchingLocation = false;
						this.isLoading = false;
						this.setLocation(position);
					},
					(error) => {
						this.isLoading = false;
						// default error says "User denied Geolocation"
						this.error = 'Unable to get location';
					}
				);
			} else {
				this.error = error.message;
			}
		},

		setLocation(position) {
			this.location.lat = position.coords.latitude;
			this.location.lng = position.coords.longitude;
			this.showLocationOnMap();
			this.convertLocationToAddress();
		},

		convertLocationToAddress() {
			var url =
				'https://maps.googleapis.com/maps/api/geocode/json?latlng=' +
				this.location.lat +
				',' +
				this.location.lng +
				'&key=AIzaSyDWXU0mgGRW6btnlX8v1tqeu2i0CJMVOOA';

			axios
				.get(url)
				.then((response) => {
					if (response.error_message) {
						this.error = response.error_message;
					} else {
						this.address.city =
							response.data.results[0].address_components[2].long_name;
						this.address.state =
							response.data.results[0].address_components[5].long_name;
						this.address.full = response.data.results[0].formatted_address;
					}
				})
				.catch((error) => {
					console.log(error);
				});
		},

		showLocationOnMap() {
			var map = new google.maps.Map(document.getElementById('map'), {
				zoom: 15,
				center: { lat: this.location.lat, lng: this.location.lng },
			});
			var marker = new google.maps.Marker({
				position: { lat: this.location.lat, lng: this.location.lng },
				map: map,
			});
		},
	},
};
</script>

<style scoped>
.btn {
	background-color: rgba(24, 135, 190, 0.738);
	border-radius: 3px;
	color: white;
	border: 0;
	padding: 10px;
	font-weight: 600;
	margin-bottom: 2rem;
	cursor: pointer;
}

.btn:hover {
	background-color: rgba(16, 101, 144, 0.738);
}

.btn:active {
	transform: scale(0.98);
	/* Scaling button to 0.98 to its original size */
	box-shadow: 3px 2px 8px 1px rgba(0, 0, 0, 0.24);
	/* Lowering the shadow */
}

.error__messages {
	color: white;
	font-size: 1.2rem;
	margin-bottom: 10px;
}

.error__messages p {
	background-color: rgba(221, 69, 42, 0.848);
	width: fit-content;
	margin: 10px auto;
	padding: 5px 20px;
}

.laoder {
	width: 500px;
	margin: 0 auto;
	text-align: center;
}

.loading {
	display: none;
}

#map {
	margin: 0 auto;
	width: 500px;
	height: 500px;
}
</style>
