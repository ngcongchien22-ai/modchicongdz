(() => {
 const pending=new Map(),geometryPending=new Map();
 window.alokLoadMesh=url=>{
  const assets=window.alokWardrobe.assets;if(assets.meshes[url])return Promise.resolve(assets.meshes[url]);
  if(pending.has(url))return pending.get(url);
  const part=assets.meshParts?.[url];if(!part)return Promise.reject(Error('Thiếu model trang phục.'));
  const loadGeometry=()=>geometryPending.get(part.url)||(()=>{const task=(async()=>{
   let cache,response;try{cache=await caches.open('fish-alok-mesh-v1');response=await cache.match(part.url);}catch{}
   if(!response){response=await fetch(part.url);if(response.ok&&cache)cache.put(part.url,response.clone()).catch(()=>{});}
   if(!response.ok)throw Error('Không tải được model trang phục.');
   const blob=await response.blob();
   const text=typeof DecompressionStream!=='undefined'?await new Response(blob.stream().pipeThrough(new DecompressionStream('gzip'))).text():new TextDecoder().decode(fflate.gunzipSync(new Uint8Array(await blob.arrayBuffer())));
   return JSON.parse(text);
  })().catch(error=>{geometryPending.delete(part.url);throw error;});geometryPending.set(part.url,task);return task;})();
  const task=loadGeometry().then(geometry=>{
   const mesh={...geometry,...part.attributes,submeshes:geometry.submeshes.map((sub,i)=>({...sub,...part.materials[i]}))};assets.meshes[url]=mesh;return mesh;
  }).catch(error=>{pending.delete(url);throw error;});pending.set(url,task);return task;
 };
})();
