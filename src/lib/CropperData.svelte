<script>
 export let detail;
</script>
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
