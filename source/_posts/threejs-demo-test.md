---
title: threejs demo test
date: 2019-08-16 20:29:41
tags:
---

以下demo展示的内容为：A cube rotating on it's Y axis-a sphere rotating around the cube

threejs里面使用的是右手坐标系：

![](https://www.blog.starmoon.tech/img/threejs.png)

```
var arWorldRoot = smoothedRoot
        //All the animations frame
        //Loading the 3D models
        var cubeGeo = new THREE.CubeGeometry(5, 5, 5, 5, 5, 5);
        var cubeMat = new THREE.MeshLambertMaterial({
            color:0xFF6600,
            wireframe:false
        });
        cubeMesh = new THREE.Mesh(cubeGeo, cubeMat);
        cubeMesh.scale.x = 0.15;
        cubeMesh.scale.y = 0.15;
        cubeMesh.scale.z = 0.15;
        cubeMesh.position.set(0, 1, 0);
        var material = new THREE.MeshBasicMaterial({
            color: 0xffff00,
            wireframe: false
        });             
        var sphere = new THREE.Mesh(new THREE.SphereGeometry(1,18,12), material);   
        sphere.position.set(0, 6, 0);

        var pivotPoint= new THREE.Object3D();
        cubeMesh.add(pivotPoint);
        pivotPoint.add(sphere);
        //Add to scene
        arWorldRoot.add(cubeMesh);

        //////////////////////////////////////////////////////////////////////////////////
        //      render the whole thing on the page
        //////////////////////////////////////////////////////////////////////////////////
        var animate = function () {
                requestAnimationFrame( animate );              
                cubeMesh.rotation.z+=0.01;
                pivotPoint.rotation.z+= 0.05;
                renderer.render( scene, camera );

            };  
        animate();var arWorldRoot = smoothedRoot
        //All the animations frame
        //Loading the 3D models
        var cubeGeo = new THREE.CubeGeometry(5, 5, 5, 5, 5, 5);
        var cubeMat = new THREE.MeshLambertMaterial({
            color:0xFF6600,
            wireframe:false
        });
        cubeMesh = new THREE.Mesh(cubeGeo, cubeMat);
        cubeMesh.scale.x = 0.15;
        cubeMesh.scale.y = 0.15;
        cubeMesh.scale.z = 0.15;
        cubeMesh.position.set(0, 1, 0);

        var material = new THREE.MeshBasicMaterial({
            color: 0xffff00,
            wireframe: false
        });             
        var sphere = new THREE.Mesh(new THREE.SphereGeometry(1,18,12), material);   
        sphere.position.set(0, 6, 0);

        var pivotPoint= new THREE.Object3D();
        cubeMesh.add(pivotPoint);
        pivotPoint.add(sphere);
        //Add to scene
        arWorldRoot.add(cubeMesh);

        //////////////////////////////////////////////////////////////////////////////////
        //      render the whole thing on the page
        //////////////////////////////////////////////////////////////////////////////////
        var animate = function () {
                requestAnimationFrame( animate );              
                cubeMesh.rotation.z+=0.01;
                pivotPoint.rotation.z+= 0.05;
                renderer.render( scene, camera );

            };  
        animate();

```