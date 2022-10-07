<script>
 import Cropper from 'cropperjs';
 import { onMount } from 'svelte';
 import '../node_modules/cropperjs/dist/cropper.min.css';
 import Navbar from './lib/Navbar.svelte';
 import RadioLabel from './lib/RadioLabel.svelte';

 const DRAG_MODE_CROP = 'crop';
 let ovb = 'inset()';
 let ovbType = 'xywh';
 let detail = {
		 x: 0,
		 y: 0,
		 width: 0,
		 height: 0,
		 scaleX: 0,
		 scaleY: 0
 };

 function onCopy(ev) {
		 ev.target.disabled = true;
		 document.getElementById('css-value').select();
		document.execCommand('copy'); 
		 ev.target.textContent = "Copied!!";
		 setTimeout(() => {
				 
				ev.target.disabled = false;
		 ev.target.textContent = "Copy to clipboard";
		 }, 2000)
 }


 function initCropper() {
		 const image = document.getElementById('image');
		 const myCropper = new Cropper(image, {
				 // The view mode of the cropper
				 viewMode: 0, // 0, 1, 2, 3
				 // The dragging mode of the cropper
				 dragMode: DRAG_MODE_CROP, // 'crop', 'move' or 'none'
				 // The initial aspect ratio of the crop box
				 initialAspectRatio: NaN,
				 // The aspect ratio of the crop box
				 aspectRatio: NaN,
				 // An object with the previous cropping result data
				 data: null,
				 // A selector for adding extra containers to preview
				 preview: '',
				 // Re-render the cropper when resize the window
				 responsive: true,
				 // Restore the cropped area after resize the window
				 restore: true,
				 // Check if the current image is a cross-origin image
				 checkCrossOrigin: true,
				 // Check the current image's Exif Orientation information
				 checkOrientation: true,
				 // Show the black modal
				 modal: true,
				 // Show the dashed lines for guiding
				 guides: true,
				 // Show the center indicator for guiding
				 center: true,
				 // Show the white modal to highlight the crop box
				 highlight: true,
				 // Show the grid background
				 background: true,
				 // Enable to crop the image automatically when initialize
				 autoCrop: true,
				 // Define the percentage of automatic cropping area when initializes
				 autoCropArea: 0.8,
				 // Enable to move the image
				 movable: true,
				 // Enable to rotate the image
				 rotatable: true,
				 // Enable to scale the image
				 scalable: true,
				 // Enable to zoom the image
				 zoomable: true,
				 // Enable to zoom the image by dragging touch
				 zoomOnTouch: true,
				 // Enable to zoom the image by wheeling mouse
				 zoomOnWheel: true,
				 // Define zoom ratio when zoom the image by wheeling mouse
				 wheelZoomRatio: 0.1,
				 // Enable to move the crop box
				 cropBoxMovable: true,
				 // Enable to resize the crop box
				 cropBoxResizable: true,
				 // Toggle drag mode between "crop" and "move" when click twice on the cropper
				 toggleDragModeOnDblclick: true,
				 // Size limitation
				 minCanvasWidth: 0,
				 minCanvasHeight: 0,
				 minCropBoxWidth: 0,
				 minCropBoxHeight: 0,
				 minContainerWidth: 200,
				 minContainerHeight: 100,
				 // Shortcuts of events
				 ready: null,
				 cropstart: null,
				 cropmove: null,
				 cropend: null,
				 crop: (event) => {
						 detail = event.detail;
						 updateOvb(); 
				 },
				 zoom: null
		 })
 }

 function updateOvbType(valueType) {

		 ovbType = valueType;
		 updateOvb();
		 
 }

 function updateOvb() {
		 /* console.log("x: ", detail.x, "y: ", detail.y, "width: ", detail.scaleX, "height: ", detail.scaleY);  */
		 const img = document.getElementById('ovb')
		 const { naturalWidth, naturalHeight } = img;
		 const { x,y,width, height } = detail;
		 let _x, _y, _width, _height;
		 let ovbValue = '';
		 if(ovbType === 'inset') {
				 let top, right, bottom, left;
				 top = Math.ceil(y);
				 left = Math.ceil(x);
				 bottom = Math.ceil(naturalHeight - (y + height));
				 right = Math.ceil(naturalWidth - (x + width));
				 ovbValue = `inset(${top}px ${right}px ${bottom}px ${left}px)`;
				 /* console.log(ovbValue); */
				 ovb = ovbValue;
				 
		 } else if(ovbType === 'rect') {

				 let top, right, bottom, left;
				 top = Math.ceil(y);
				 left = Math.ceil(x);
				 bottom = Math.ceil((y + height));
				 right = Math.ceil((x + width));
				 ovbValue = `rect(${top}px ${right}px ${bottom}px ${left}px)`;
				 /* console.log(ovbValue); */
				 ovb = ovbValue;
		 } else {
				 _x = Math.ceil(x);
				 _y = Math.ceil(y);
				 _width = Math.ceil(width);
				 _height = Math.ceil(height);
				 ovbValue = `xywh(${_x}px ${_y}px ${_width}px ${_height}px)`;
				 /* console.log(ovbValue); */
				 ovb = ovbValue;
		 }

		 img.style.objectViewBox = ovbValue;
		 
 }

 let input;
 let container;
 let image;
 let preview;
 let placeholder;
 let showImage = false;

 

 function onChange() {
     const file = input.files[0];
		 
     if (file) {
				 showImage = true;

				 const reader = new FileReader();
				 reader.addEventListener("load", function () {
						 image.setAttribute("src", reader.result);
						 preview.setAttribute("src", reader.result);
						initCropper();
				 });
				 reader.readAsDataURL(file);

				 
				 return;
     } 
		 showImage = false; 
 }
</script>

<Navbar/>
<div class="relative flex flex-wrap items-center justify-between px-2 py-3">
		<div class="container mx-auto">
				<div class="flex gap-2">
		<div class="p-4 shadow rounded bg-white w-2/3">
								<p class="text-blue-700 mb-2 font-bold">CSS property:</p>
				<textarea id="css-value" rows="1" class="w-full text-indigo-700 text-2xl bg-indigo-50 px-4 py-2 rounded">object-view-box: {ovb};</textarea>
				<div class="flex">
						<input type="file" bind:this={input} on:change={onChange}  class="block my-2 text-sm text-slate-500
												 file:mr-4 file:py-2 file:px-4
												 file:rounded-full file:border-0
												 file:text-xl file:font-semibold
												 file:bg-indigo-50 file:text-indigo-700
												 file:cursor-pointer
												 hover:file:bg-indigo-100">
				<button on:click={onCopy} class="block bg-indigo-500 text-white text-xl my-2 px-4 py-2 mx-auto
								rounded hover:bg-indigo-600
								disabled:bg-red-500 disabled:cursor-not-allowed">Copy to Clipboard</button>
				</div>
		</div>
		<div class="w-1/3 bg-white p-2 shadow rounded">
<p class="text-blue-700 mb-2 font-bold">Value type:</p>
								<div class=" my-2">
										<RadioLabel><input class="mr-2" type="radio" name="ovb-type" on:click={() => updateOvbType("inset")}  />inset</RadioLabel>
										<RadioLabel><input class="mr-2" type="radio" name="ovb-type" on:click={() => updateOvbType("rect")} />rect</RadioLabel>
										<RadioLabel><input class="mr-2" type="radio" name="ovb-type" on:click={() => updateOvbType("xywh")} checked />xywh</RadioLabel>
								</div>

		</div>
				</div>

		<main class="flex flex-row gap-4 p-4 mx-auto">
				<div class="max-w-[640px] max-h-[640px] mx-auto my-4 overflow-scroll">
						{#if showImage}
						<img id="image" bind:this={image} />
						{:else}
						<div class="flex items-center justify-center bg-slate-100 rounded shadow p-4 min-w-[640px] min-h-[640px]">
								Select an image
						</div>
						{/if}
				</div>
				<div>
												<div class="preview bg-white shadow rounded p-4">
														{#if showImage}
														<img id="ovb" bind:this={preview} />
														{:else}
						<div class="bg-slate-100 rounded shadow p-4 flex items-center justify-center min-w-[500px] min-h-[500px]">
								Image preview here
						</div>
														{/if}
												</div>
												<div id="output" class="bg-white shadow p-4 rounded">
								
								<p class="text-indigo-500 font-bold">Cropper data:</p>
								<table class="table my-2 border-separate">
										<tr>
												<td class="bg-slate-100 p-2 rounded font-bold text-slate-500">x:</td>
												<td class="bg-slate-100 p-2 rounded text-slate-600">{detail.x}</td>
										</tr>
										<tr>
												<td class="bg-slate-100 p-2 rounded font-bold">y:</td>
												<td class="bg-slate-100 p-2 rounded text-slate-500">{detail.y}</td>
										</tr>
										<tr>
												<td class="bg-slate-100 p-2 rounded font-bold">width:</td>
												<td class="bg-slate-100 p-2 rounded text-slate-500">{detail.width}</td>
										</tr>
										<tr>
												<td class="bg-slate-100 p-2 rounded font-bold">height:</td>
												<td class="bg-slate-100 p-2 rounded text-slate-500">{detail.height}</td>
										</tr>
								</table>
												</div>

				</div>

		</main>
</div>
</div>

<style>


 img {
		 display: block;
		 max-width: 100%;
 }

 .preview {
		 max-width: 640px;
		 margin: 20px auto;
		 overflow: scroll;
		 max-height: 640px;
 }

 #ovb {
		 object-fit: cover;
		 display: block;
		 max-width: 100%;
 }
</style>
