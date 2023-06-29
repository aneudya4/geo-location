<template>
	<div id="app">
		<div v-if="!file">
			<div
				:class="['dropZone', dragging ? 'dropZone-over' : '']"
				@dragenter="dragging = true"
				@dragleave="dragging = false"
			>
				<div class="dropZone-info" @drag="onChange">
					<span class="fa fa-cloud-upload dropZone-title"></span>
					<span class="dropZone-title">Drop file or click to upload</span>
					<div class="dropZone-upload-limit-info">
						<div>extension support: txt</div>
						<div>maximum file size: 5 MB</div>
					</div>
				</div>
				<input type="file" @change="onChange" />
			</div>
		</div>
		<div v-else class="dropZone-uploaded">
			<div class="dropZone-uploaded-info">
				<span class="dropZone-title">Uploaded</span>
				<button
					type="button"
					class="btn btn-primary removeFile"
					@click="removeFile"
				>
					Remove File
				</button>
			</div>
		</div>
		<!-- 
		<div class="uploadedFile-info">
			<div>fileName: {{ file.name }}</div>
			<div>fileZise(bytes): {{ file.size }}</div>
			<div>extension：{{ extension }}</div>
		</div> -->
	</div>
</template>

<script>
export default {
	name: 'User',

	data() {
		return {
			file: '',
			dragging: false,
		};
	},
	methods: {
		onChange(e) {
			var files = e.target.files || e.dataTransfer.files;

			if (!files.length) {
				this.dragging = false;
				return;
			}

			this.createFile(files[0]);
		},
		createFile(file) {
			if (!file.type.match('pdf.*')) {
				alert('please select txt file');
				this.dragging = false;
				return;
			}

			if (file.size > 115000000) {
				alert('please check file size no over 15 MB.');
				this.dragging = false;
				return;
			}

			this.file = file;
			console.log(this.file);
			this.dragging = false;
		},
		removeFile() {
			this.file = '';
		},
	},
	computed: {
		extension() {
			return this.file ? this.file.name.split('.').pop() : '';
		},
	},
};
</script>

<style scoped>
.dropZone {
	width: 80%;
	height: 200px;
	position: relative;
	border: 2px dashed #eee;
}

.dropZone:hover {
	border: 2px solid #2e94c4;
}

.dropZone:hover .dropZone-title {
	color: #1975a0;
}

.dropZone-info {
	color: #a8a8a8;
	position: absolute;
	top: 50%;
	width: 100%;
	transform: translate(0, -50%);
	text-align: center;
}

.dropZone-title {
	color: #787878;
}

.dropZone input {
	position: absolute;
	cursor: pointer;
	top: 0px;
	right: 0;
	bottom: 0;
	left: 0;
	width: 100%;
	height: 100%;
	opacity: 0;
}

.dropZone-upload-limit-info {
	display: flex;
	justify-content: flex-start;
	flex-direction: column;
}

.dropZone-over {
	background: #5c5c5c;
	opacity: 0.8;
}

.dropZone-uploaded {
	width: 80%;
	height: 200px;
	position: relative;
	border: 2px dashed #eee;
}

.dropZone-uploaded-info {
	display: flex;
	flex-direction: column;
	align-items: center;
	color: #a8a8a8;
	position: absolute;
	top: 50%;
	width: 100%;
	transform: translate(0, -50%);
	text-align: center;
}

.removeFile {
	width: 200px;
}
</style>
