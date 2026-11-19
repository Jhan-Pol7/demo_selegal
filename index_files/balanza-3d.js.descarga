/* SELEGAL — indexed-by-profile brasswork; geometry is rendered in perspective. */
const stage = document.querySelector('[data-scales]');
if (stage) mount(stage);

function mount(stage) {
  const canvas = stage.querySelector('canvas');
  const ctx = canvas.getContext("2d");
  if (!ctx) return;
  let yaw = -0.19, pitch = 0.13, pointer = null;
  const vertices = [];
  const brass=[0.71,0.44,0.126], highlight=[0.86,0.626,0.26], bronze=[0.39,0.22,0.075], stone=[0.056,0.105,0.085];
  const add=(a,b)=>a.map((v,i)=>v+b[i]), sub=(a,b)=>a.map((v,i)=>v-b[i]), mul=(a,s)=>a.map(v=>v*s);
  const cross=(a,b)=>[a[1]*b[2]-a[2]*b[1],a[2]*b[0]-a[0]*b[2],a[0]*b[1]-a[1]*b[0]];
  const unit=a=>mul(a,1/(Math.hypot(...a)||1));
  const vertex=(p,n,c)=>vertices.push(...p,...n,...c);
  function surface(rows,color) {
    for(let i=1;i<rows.length;i++)for(let j=1;j<rows[i].length;j++){
      const a=rows[i-1][j-1], b=rows[i-1][j], c=rows[i][j], d=rows[i][j-1];
      for(const v of [a,b,c,c,d,a])vertex(v[0],v[1],color);
    }
  }
  // Turn a jeweller's profile around the vertical axis; use smooth radial normals.
  function turn(x,z,profile,color){
    for(let k=1;k<profile.length;k++){
      const [r,y]=profile[k-1],[s,h]=profile[k];const rows=[];
      for(const [rad,py] of [[r,y],[s,h]]){
        const row=[];for(let n=0;n<=72;n++){const a=n*Math.PI/36,C=Math.cos(a),S=Math.sin(a);
          row.push([[x+rad*C,py,z+rad*S],unit([(h-y)*C,r-s,(h-y)*S])]);}rows.push(row);
      }surface(rows,color);
    }
  }
  function tube(a,b,r,color,facets=10){
    const axis=unit(sub(b,a)),u=unit(cross(axis,Math.abs(axis[1])>.9?[1,0,0]:[0,1,0])),v=cross(axis,u);
    surface([a,b].map(p=>Array.from({length:facets+1},(_,i)=>{const t=2*Math.PI*i/facets,N=add(mul(u,Math.cos(t)),mul(v,Math.sin(t)));return [add(p,mul(N,r)),N];})),color);
  }
  function path(points,r,color){for(let i=1;i<points.length;i++)tube(points[i-1],points[i],r,color);}
  function loop(cx,cy,cz,rx,ry,angle,r,color){
    path(Array.from({length:17},(_,i)=>{const t=i*Math.PI/8;return [cx+rx*Math.cos(t)*Math.cos(angle),cy+ry*Math.sin(t),cz+rx*Math.cos(t)*Math.sin(angle)];}),r,color);
  }
  turn(0,0,[[0,.04],[.57,.04],[.65,.09],[.65,.17],[.60,.21],[.48,.23],[0,.23]],stone);
  turn(0,0,[[.60,.18],[.63,.19],[.60,.21]],highlight);
  turn(0,0,[[0,.23],[.47,.23],[.49,.26],[.47,.29],[.36,.315],[.32,.37],[.22,.39],[.20,.47],[.14,.51],[.105,.62],[.088,2.68],[.12,2.73],[.14,2.78],[.125,2.84],[.096,2.87],[.092,3.19],[.13,3.23],[.12,3.28],[.07,3.31],[.06,3.40],[.10,3.45],[.105,3.50],[.07,3.55],[0,3.64]],brass);
  for(const y of [.49,.56,2.66,2.84,3.23])turn(0,0,[[.10,y],[.133,y+.012],[.132,y+.03],[.10,y+.04]],highlight);
  // A crowned beam with a swept arch and individually linked suspension chains.
  for(const sign of [-1,1]){
    const beam=Array.from({length:35},(_,i)=>{const t=i/34;return [sign*(.04+1.35*t),3.04-.27*Math.sin(t*Math.PI)+.05*t,0];});
    path(beam,.045,brass);
    path(beam.map(([x,y,z],i)=>[x,y+.066*Math.sin(i/34*Math.PI),z+.008]),.021,highlight);
    const cx=1.39*sign,top=[cx,3.055,0],bowlY=1.65;
    loop(cx,3.11,0,.075,.085,0,.022,highlight);
    for(let j=0;j<3;j++){
      const a=j*Math.PI*2/3+Math.PI/6;
      const end=[cx+.47*Math.cos(a),bowlY,.47*Math.sin(a)];
      const delta=sub(top,end);
      tube(end,top,.008,bronze,6);
      for(let k=0;k<22;k++){const p=add(end,mul(delta,(k+.5)/22));loop(...p,.016,.037,a+(k%2)*Math.PI/2,.0055,highlight);}
    }
    turn(cx,0,[[.002,bowlY-.20],[.16,bowlY-.181],[.29,bowlY-.144],[.395,bowlY-.084],[.487,bowlY-.009],[.49,bowlY+.012],[.478,bowlY+.021],[.46,bowlY+.001],[.39,bowlY-.066],[.28,bowlY-.12],[.13,bowlY-.151],[0,bowlY-.157]],brass);
    turn(cx,0,[[.478,bowlY-.002],[.493,bowlY+.004],[.492,bowlY+.02],[.478,bowlY+.024]],highlight);
  }
  try {
    let render;
      // Software projection and depth buffer for browsers without a GPU context.
      const mesh=new Float32Array(vertices),screen=new Float32Array(mesh.length/9*6);
      render=()=>{
        const w=canvas.clientWidth,h=canvas.clientHeight;if(!w||!h)return;
        const ratio=Math.min(1,620/Math.max(w,h)),W=Math.round(w*ratio),H=Math.round(h*ratio);
        if(canvas.width!==W||canvas.height!==H){canvas.width=W;canvas.height=H;}
        const img=ctx.createImageData(W,H),pixels=img.data,depth=new Float32Array(W*H),cy=Math.cos(yaw),sy=Math.sin(yaw),cp=Math.cos(pitch),sp=Math.sin(pitch),scale=Math.min(3.45,3.25*w/h)*H/2;
        for(let i=0,j=0;i<mesh.length;i+=9,j+=6){
          const x=cy*mesh[i]+sy*mesh[i+2],y=mesh[i+1]-1.82,z=-sy*mesh[i]+cy*mesh[i+2],d=1/(7.4-sp*y-cp*z);
          screen[j]=W/2+x*d*scale;screen[j+1]=H/2-(y*cp-z*sp)*d*scale;screen[j+2]=d;
          const nx=cy*mesh[i+3]+sy*mesh[i+5],ny=mesh[i+4],nz=-sy*mesh[i+3]+cy*mesh[i+5],v=ny*sp+nz*cp,rx=2*v*nx,ry=2*v*ny-sp;
          const reflection=.32+.45*Math.max(0,ry)+1.8*Math.exp(-8*Math.abs(rx+.72)-1.4*Math.abs(ry-.27))+1.35*Math.exp(-10*Math.abs(rx-.74)-2*Math.abs(ry-.3))+.20*Math.max(0,-.45*nx+.8*ny+.4*nz);
          for(let c=0;c<3;c++)screen[j+3+c]=255*Math.pow(mesh[i+6+c]*reflection+.035,.72);
        }
        for(let j=0;j<screen.length;j+=18){
          const ax=screen[j],ay=screen[j+1],bx=screen[j+6],by=screen[j+7],cx=screen[j+12],cy2=screen[j+13],area=(by-cy2)*(ax-cx)+(cx-bx)*(ay-cy2);if(Math.abs(area)<.0001)continue;
          const xmin=Math.max(0,Math.floor(Math.min(ax,bx,cx))),xmax=Math.min(W-1,Math.ceil(Math.max(ax,bx,cx))),ymin=Math.max(0,Math.floor(Math.min(ay,by,cy2))),ymax=Math.min(H-1,Math.ceil(Math.max(ay,by,cy2)));
          for(let y=ymin;y<=ymax;y++)for(let x=xmin;x<=xmax;x++){const u=((by-cy2)*(x+.5-cx)+(cx-bx)*(y+.5-cy2))/area,v=((cy2-ay)*(x+.5-cx)+(ax-cx)*(y+.5-cy2))/area,l=1-u-v;if(u<0||v<0||l<0)continue;const k=y*W+x,d=u*screen[j+2]+v*screen[j+8]+l*screen[j+14];if(depth[k]>=d)continue;depth[k]=d;
            for(let c=0;c<3;c++)pixels[4*k+c]=u*screen[j+3+c]+v*screen[j+9+c]+l*screen[j+15+c];pixels[4*k+3]=255;
          }
        }ctx.putImageData(img,0,0);
      };
    const paint=render;let frame=0;render=()=>{if(!frame)frame=requestAnimationFrame(()=>{frame=0;paint();});};
    paint();stage.classList.add('has-model');canvas.tabIndex=0;new ResizeObserver(render).observe(stage);
    canvas.addEventListener('pointerdown',e=>{pointer=[e.clientX,e.clientY];canvas.setPointerCapture(e.pointerId);});
    canvas.addEventListener('pointermove',e=>{if(!pointer)return;yaw+=(e.clientX-pointer[0])*.012;pitch=Math.max(-.08,Math.min(.46,pitch+(e.clientY-pointer[1])*.005));pointer=[e.clientX,e.clientY];render();});
    canvas.addEventListener('lostpointercapture',()=>pointer=null);
    canvas.addEventListener('keydown',e=>{if(!['ArrowLeft','ArrowRight','Home'].includes(e.key))return;e.preventDefault();yaw=e.key==='Home'?-.19:yaw+(e.key==='ArrowLeft'?-.18:.18);render();});
    canvas.addEventListener('webglcontextlost',()=>{stage.classList.remove('has-model');canvas.tabIndex=-1;});
  } catch {stage.classList.remove('has-model');}
}
