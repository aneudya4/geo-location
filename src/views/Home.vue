<template>
	<UserLocation />
	<!-- <User /> -->
</template>

<script>
// @ is an alias to /src
import User from '@/pages/User.vue';

import UserLocation from '@/pages/UserLocation.vue';

export default {
	name: 'Home',
	components: {
		UserLocation,
		User,
	},
};
</script>
