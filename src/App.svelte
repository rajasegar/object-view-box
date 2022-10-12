<script>
 import Cropper from 'cropperjs';
 import '../node_modules/cropperjs/dist/cropper.min.css';
 import Navbar from './lib/Navbar.svelte';
 import RadioLabel from './lib/RadioLabel.svelte';
 import CropperData from './lib/CropperData.svelte';
 import cropperOptions from './lib/cropper-options.js';

 const isChrome = navigator.userAgent.includes('Chrome'); 
 let myCropper = undefined;
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

 let input;
 let container;
 let image;
 let preview;
 let placeholder;
 let showImage = false;

 let naturalWidth;
 let naturalHeight;

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


 function clearImage() {
		 if(myCropper) {
				 myCropper.destroy();
				 myCropper = undefined;
		 }
		 showImage = false;
		 detail = {
				 x: 0,
				 y: 0,
				 width: 0,
				 height: 0,
				 scaleX: 0,
				 scaleY: 0
		 };

  ovb = 'inset()';
  ovbType = 'xywh';
 }
 function initCropper() {
		 const image = document.getElementById('image');
		 if(myCropper) {
				 myCropper.destroy();
		 }
		 cropperOptions.crop = (event) => {
				detail = event.detail;
				updateOvb(); 
		};

		 myCropper = new Cropper(image, cropperOptions);
 }

 function updateOvbType(valueType) {
		 ovbType = valueType;
		 updateOvb();
 }

 function updateOvb() {
		 let img;
		 
		 if(isChrome) { 
				img = document.getElementById('ovb')
				naturalWidth = img.naturalWidth;
				naturalHeight = img.naturalHeight;
				}
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

		 if(isChrome) {
				 img.style.objectViewBox = ovbValue;
		 }
		 
 }

  

 function onChange() {
     const file = input.files[0];
		 
     if (file) {
				 showImage = true;

				 const reader = new FileReader();
				 reader.addEventListener("load", function () {
						 if(!isChrome) {
								const tempImg = document.createElement('img');
						 tempImg.setAttribute("src", reader.result);
						 console.log(tempImg.naturalWidth, tempImg.naturalHeight )
						 naturalWidth = tempImg.naturalWidth;
						 naturalHeight = tempImg.naturalHeight;
						 }
						 image.setAttribute("src", reader.result);
						 if(isChrome) {
								preview.setAttribute("src", reader.result);
						 }
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
																				<button disabled={!showImage} on:click={onCopy} class="block bg-indigo-500 text-white text-xl my-2 px-4 py-2 mx-auto
														rounded hover:bg-indigo-600
														disabled:bg-red-500 disabled:cursor-not-allowed disabled:opacity-50">Copy to Clipboard</button>
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
						<div class="max-w-[640px] max-h-[640px] mx-auto my-4 overflow-scroll relative">
								{#if showImage}
										<img id="image" bind:this={image} />
										<button type="button" on:click={clearImage} class="absolute uppercase top-1 right-1 bg-red-500 hover:bg-red-700 text-white rounded px-2 py-1 text-sm z-100">Clear</button>
								{:else}
										<div class="flex items-center justify-center bg-slate-100 rounded shadow p-4 min-w-[640px] min-h-[640px]">
												<!-- <p>Select an image</p> -->
												<div><input type="file" bind:this={input} on:change={onChange}  class="block my-2 text-sm text-slate-500
																		 file:mr-4 file:py-2 file:px-4
																		 file:rounded-full file:border-0
																		 file:text-xl file:font-semibold
																		 file:bg-indigo-100 file:text-indigo-700
																		 file:cursor-pointer
																		 hover:file:bg-indigo-200">
												</div>

										</div>
								{/if}
						</div>
						<div>
								<div class="preview bg-white shadow rounded p-4">
										{#if isChrome}
										{#if showImage}
												<img id="ovb" bind:this={preview} />
										{:else}
												<div class="bg-slate-100 rounded shadow p-4 flex items-center justify-center min-w-[500px] min-h-[500px]">
														Image preview here
												</div>
										{/if}
										{:else}
										<div class="bg-red-100 text-red-500 px-3 py-4 rounded">
												<p class="font-bold text-center text-xl"> Preview not available. </p>
												<p class="text-sm">Your browser does not support 'object-view-box' CSS property.</p>
										</div>
										{/if}
								</div>
								<CropperData {detail} />
						</div>

				</main>
		</div>
</div>
<div class="text-center">
		<p class="text-indigo-700"> Made with <span class="text-red-500">&hearts;</span> by <a class="text-blue-500 hover:text-blue-700 hover:drop-shadow" target="_blank" href="https://twitter.com/@rajasegar_c">Rajasegar Chandran</a></p>
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
