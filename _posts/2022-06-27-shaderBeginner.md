---
layout: post
title:  "Shaders for Unity - Ultra Beginners"
date:   2022-06-27 14:29:24 +0200
categories: shaders Unity
---

# Shaders for Unity

This note rely on the following videos : 
- [Shaders for Game Dev (Part 1)](https://www.youtube.com/watch?v=kfM-yu0iQBk&t=8350s)
- [Shaders for Game Dev (Part 2)](https://www.youtube.com/watch?v=mL8U8tIiRRg)
- [Shaders for Game Dev (Part 3)](https://www.youtube.com/watch?v=E4PHFnvMzFc)
 
---

## Structure d’un fichier .shader
It must be noted that two languages are used in Unity Shaders : 
* ShaderLab : for the link between Unity and Shader (declaring variables etc …) 
* HLSL : For the shader logic.
Here we got the main structure of a Unity Shader : 

``` clike
Shader: "Custom/MyShader"
{
	Properties
	{
		_Color("Color", Color) = (0,0,0,0)
		//Other variable for Unity inspector
	}
	Subshader
	{
		Tags{"RenderType" = "Opaque"}
		LOD 200
		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			
			#include "UnityCG.cginc";				

			struct appData
			{
				float4 vertex : POSITIONS;
				float2 uv0 : TEXCOORD0; //uv channel 0
				//float4 normal : NORMALS;
				//float4 tangent : TANGENTS;
				//float2 uv1: TEXCOORD1; //uv channel 1 
				/// ...
			}
			
			struct interpolators
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0; //Channel to pass data to fragment (not necessarily vus)
				//... Other TEXCOORD channels
			}
			
			float4 _COLORS; //Unity variables (need to have the same name than in properties)

			interpolators vert(appData a)
			{
				interpolators o; 
				o.pas = UnityObjectToClipPos(a.vertex); //Pass vertex to the clip space coordinates
				o.uv = a.uv //Only passthrough
				// Deal here with your vertex modifications and pre calculations
				return o;
			}
			
			fixed4 frag(interpolators i) : SV_Target
			{
				//Do some processing for your fragment
				return fixed4(i.uv,0,0); //display the uv channel
			}
			
		}
	}
}

```

It must be distinguished in two parts :
* Unity related declarations : Properties, Subshader (in /ShaderLab/)
* The shader logic : Pass (in /HLSL/)

### Shader Lab part
Here we declare the variables that would be set in the unity inspector. It is also in the SubShader that we declare the tag that describe the type of shader that is written (Opaque, Transparent, ...) 
### Shader content (HLSL)
First, we declare the functions that are the core of the shader pipeline : vertex and fragment. To put it in a nutshell, here is how both functions works together [image:CAAE1A0A-B299-46BA-9ECD-63483E0CDBD0-24531-0000056967309096/bear_sketch@2x.png]
