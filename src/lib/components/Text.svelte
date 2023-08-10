<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { HTML } from '@threlte/extras';
	import { ShaderMaterial, type Mesh, DoubleSide } from 'three';

	// Attributs
	export let text: string;
	export let button: Mesh;
	export let isHovered: Boolean;
	$: color = isHovered ? 'white' : 'black';
	let title: HTMLElement;
	const material = new ShaderMaterial({
		transparent: true,
		uniforms: {
			uTime: { value: 0 }
		},
		side: DoubleSide,
		vertexShader: `
		varying vec2 vUv;
			void main()
			{
				vUv= uv;
				gl_Position = projectionMatrix * modelViewMatrix * vec4(position,1.0);
			}
			`,
		fragmentShader: `
		varying vec2 vUv;
		uniform float uTime;





			void main()
			{
				float aspect = 1.0;
				// Aspect ratio
                vec2 uv = vUv * 2.0 - 1.0;
                float value;
			    float rot = radians(45.0); // radians(45.0*sin(uTime));
   				mat2 m = mat2(cos(rot), -sin(rot), sin(rot), cos(rot));
   				uv  = m * uv;
  			    uv += vec2(0.5, 0.5*aspect);
  			    uv.y+=0.5*(1.0-aspect);
  			    vec2 pos = 10.0*uv;
 			    vec2 rep = fract(pos);
   			    float dist = 2.0*min(min(rep.x, 1.0-rep.x), min(rep.y, 1.0-rep.y));
   			    float squareDist = length((floor(pos)+vec2(0.5)) - vec2(5.0) );
    
 			    float edge = sin(uTime-squareDist*0.5)*0.5+0.5;
				
    			edge = (uTime-squareDist*0.5)*0.5;
   			    edge = 2.0*fract(edge*0.5);
   		 //value = 2.0*abs(dist-0.5);
  	     //value = pow(dist, 2.0);
  			    value = fract (dist*2.0);
   			    value = mix(value, 1.0-value, step(1.0, edge));
    //value *= 1.0-0.5*edge;
   			    edge = pow(abs(1.0-edge), 2.0);
    
    //edge = abs(1.0-edge);
  			    value = smoothstep( edge-0.05, edge, 0.95*value);
    
    
  		        value += squareDist*.1;
    //fragColor = vec4(value);
    gl_FragColor = mix(vec4(1.0,1.0,1.0,1.0),vec4(0.5,0.75,1.0,1.0), value);
    gl_FragColor.a = 0.95*clamp(value, 0.0, 1.0);
			}
			`
	});

	useFrame((delta) => {
		title.style.color = color;
		material.uniforms.uTime.value = delta.clock.elapsedTime;
	});
</script>

<T is={button} position={[-12.5, 10.5, -6.7]} rotation.y={Math.PI / 2}>
	<T.PlaneGeometry args={[25, 25]} />
	<T is={material} transparent opacity={0} />

	<HTML transform occlude position.y={0} position.z={0.2} scale={0.5}>
		<p bind:this={title}>{text}</p>
	</HTML>
</T>

<style>
	p {
		font-size: 5em;
		text-align: center;
	}
</style>
