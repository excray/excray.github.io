---
author: gvivekcbe
comments: true
date: 2011-06-13 05:44:15+00:00
layout: post
slug: fish-schooling
title: Fish schooling
wordpress_id: 45
---

Wrote a kernel for fish schooling. The opencl kernel is below.

[sourcecode language="cpp"]
<pre>

__kernel

void

fishy_simulation ( __global float4* pos,
				   __global float4* dirvec,
				   float speed,
				   float deltaTime,
				   float inner_radius,
				   float outer_radius,
				   int num_fishes,
				   __local float4* localPos,
				   __local float4* localdir,
				   __global float4* newPosition,
				   __global float4* newDirection,
				   __global float4* dbgArray
				   )
{

float wa = 1.0f;
float wo = 1.0f;

unsigned int tid = get_local_id(0);
unsigned int gid = get_global_id(0);
unsigned int localSize = get_local_size(0);

unsigned int numTiles = num_fishes/localSize;

float4 myPos = pos[gid];
float4 mydir = dirvec[gid];


float4 vec   = (float4)(0.0f, 0.0f, 0.0f, 0.0f);
float4 temp1 = (float4)(0.0f, 0.0f, 0.0f, 0.0f);
float4 temp2 = (float4)(0.0f, 0.0f, 0.0f, 0.0f);
int inc = 0;
int ouc = 0;


	for(int i = 0; i < numTiles; ++i)
	{
        // load one tile into local memory
        int idx = i * localSize + tid;
        localPos[tid] = pos[idx];
		localdir[tid] = dirvec[idx];

        // Synchronize to make sure data is available for processing
        barrier(CLK_LOCAL_MEM_FENCE);
	
	    //calculate the change in position due to each fish
		for(int j = 0; j < localSize; ++j)
	    {
			float4 t = localPos[j] - myPos;
			temp1 = t;
			float len = sqrt ( t.x * t.x + t.y * t.y + t.z * t.z );

			if ( len != 0.0f )
			{
				if ( len <= inner_radius )
				{
					t.x = -(temp1.x/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
					t.y = -(temp1.y/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
					t.z = -(temp1.z/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
					vec += t;
					inc++;
				}

				if ( len > inner_radius && len <= outer_radius )
				{
				
					t.x = (temp1.x/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
					t.y = (temp1.y/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
					t.z = (temp1.z/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
				
					temp2 = localdir[j];

					temp1.x = (temp2.x/( sqrt (temp2.x *temp2.x + temp2.y * temp2.y + temp2.z * temp2.z ) ));
					temp1.y = (temp2.y/( sqrt (temp2.x *temp2.x + temp2.y * temp2.y + temp2.z * temp2.z ) ));
					temp1.z = (temp2.z/( sqrt (temp2.x *temp2.x + temp2.y * temp2.y + temp2.z * temp2.z ) ));

					t = wa * t;
					temp1 = wo * temp1;
				
					vec = vec + t + temp1;					
					ouc++;
				}
			}
		}
		  // Synchronize to make sure data is available for processing
        //barrier(CLK_LOCAL_MEM_FENCE);
	}

	float len = sqrt ( vec.x * vec.x + vec.y * vec.y + vec.z * vec.z );

	if ( len == 0.0f )
	{
		vec = mydir;
	}

	temp1 = vec;
	
	vec.x = (temp1.x/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
	vec.y = (temp1.y/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));
	vec.z = (temp1.z/( sqrt (temp1.x *temp1.x + temp1.y * temp1.y + temp1.z * temp1.z ) ));

	
	newPosition[gid] = myPos + speed * vec * deltaTime;
	newDirection[gid] = vec;

	
	if ( newPosition[gid].x > 45 || newPosition[gid].x < -45 ) 
	{
		newDirection[gid].x = -newDirection[gid].x;
	}

	if ( newPosition[gid].y > 45 || newPosition[gid].y < -45 )
	{
		newDirection[gid].y = -newDirection[gid].y;
	}

	if ( newPosition[gid].z > 45 || newPosition[gid].z < -45 )
	{
		newDirection[gid].z = -newDirection[gid].z;
	}

	dbgArray[0] = pos[0];
	dbgArray[1] = newPosition[0];
	dbgArray[2] = inc;
	dbgArray[3] = ouc;
	if ( gid == 0)
	{
		dbgArray[4] = vec;
	}
}</pre>
[/sourcecode]




