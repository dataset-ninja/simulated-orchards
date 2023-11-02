**Simulated-Orchards** presents a dataset designed explicitly for object detection tasks, featuring 1499 images containing a total of 44885 labeled objects all falling within a singular class — *apple*. Notably, this dataset is generated systematically through a tool developed in the Unity 3D game engine, allowing for the systematic creation of simulated datasets. The focus on a singular class, in this case, apples, caters to applications in object detection, offering a rich resource for training models to identify and locate apples within simulated orchard environments, providing a valuable asset for agricultural and computer vision research.

For the systematic generation of simulated datasets, authors propose a tool made in the [Unity 3D game engine](https://unity.com/). This software system can be configured to adapt the resulting dataset to specifically fit the application. After the configurations are chosen, the software will proceed to automatically create a dataset to be used as training data for object detection networks.

## Simulating apple orchards in Unity

To generate realistic data, a simulator that mimics an apple orchard is needed. This should reflect the scenario in the real-world. Authors' tool achieves this by using components supplied by the Unity 3D game engine. The camera component is used to simulate an RGB camera in a 3D scenario. 

To simulate the ‘orchard’ component, authors' simulator uses a 2D plane with an image of a real-world orchard projected on it. This strategy allows to simulate a representative orchard without the need for a true-to-life 3D reconstruction of an orchard, which would take a considerably larger amount of time and effort to realise. 

<img src="https://github.com/dataset-ninja/simulated-orchards/assets/123257559/24ab828e-7582-41b6-b0fb-66e2977f1cfa" alt="image" width="500">

<span style="font-size: smaller; font-style: italic;">Simulated apple orchard with 2D plane with projected image and 3D textured apple models.</span>

The simulator uses textured 3D models that are positioned between the 2D plane and the camera to simulate apples. The placements of the 3D apples are randomly determined. A selection of apple models that represent a wide range of varieties was used.

<img src="https://github.com/dataset-ninja/simulated-orchards/assets/123257559/584d4cde-25e1-4858-9bfe-aae1afecacd6" alt="image" width="800">

<span style="font-size: smaller; font-style: italic;">Textured 3D apples used in the simulation.</span>

## Camera transformation 

By altering the camera transform for every data point the data will be generated from different angles and distances. Our tool does this by generating pseudo-random transforms. Because our simulator uses a 2D background plane with 3D apples positioned in front of it, there is a limited space of transforms for the camera that would produce valid data. In this case, valid data is data where: 
- Every pixel on the RGB image is projected from the 2D background or an apple.
- The camera is facing the front-side of the 2D plane.
- Apples are clearly visible (i.e. not occluded, fully in frame of the camera, sufficiently sized to be recognizable).

<img src="https://github.com/dataset-ninja/simulated-orchards/assets/123257559/222f2016-fd1c-4f1c-af9f-da3c0eca8673" alt="image" width="800">

<span style="font-size: smaller; font-style: italic;">Invalid camera positions: (a) Camera is too close to the 2D plane, causing the apples to be not clearly visible. (b) Camera is oriented downwards causing pixels not projected from the 2D background plane or an apple to appear in the camera image. (c) Camera is too far from the 2D plane, causing the apples not to be clearly visible.</span>

Limits of orientations and positions can be configured in the tool. The generator then generates random values to calculate transforms on the linear interpolants between these limits. Furthermore, our tool includes a minimum and maximum distance between the 2D plane and the camera. This guarantees the generated camera transforms are ‘valid’ according to the conditions described above.

## Apple positions

New 3D positions for apples are generated after a specified number of data points. This configurable number determines the generation. For the first method, a random uniform distribution creates x and y-coordinates within the target resolution of the image data. Back-projection and Unity’s raycasting system are utilized to establish the 3D position on the 2D background plane corresponding to the x and y-coordinates on the camera frame.

If the smallest distance between the generated position and other generated positions is less than the diameter of the apples (adjusted for maximum overlap), the generated position is discarded to ensure minimal overlap among apples.

In the second method, 2D coordinates of the centers of all apples are extracted from the instance segmentation masks of the [APPLE MOTS dataset](https://ieeexplore.ieee.org/abstract/document/9857971/). Only segmentation masks from scenarios featuring a line of apple trees perpendicular to the camera are considered. These positions are stored per segmentation mask. During new position generation, the tool randomly selects a segmentation mask to utilize the positions from. Similar to the first method, back-projection and Unity’s raycasting system are employed to determine the 3D position on the 2D background plane corresponding to the x-coordinates and y-coordinates derived from the extracted positions on the image frame.

## Augmenting data

To ensure the generated data reflects real-world scenarios, the generator tool incorporates what are known as augmentations. These augmentations can be enabled or disabled to diversify the datasets.

In the standard scenario (where no augmentations are applied), all apples within the simulator share identical 3D models, textures, scales, rotations, and z-axis coordinates. The lighting remains consistent across the dataset. The variations within the dataset are limited to changes in apple positions, quantities, and the camera's transformation.

| Augmentation          | Description                                                                      |
|-----------------------|----------------------------------------------------------------------------------|
| rotation              | Gives a random orientation to every apple.                                      |
| scale                 | Gives a random scale to every apple.                                             |
| depth                 | Gives a random z-coordinate (depth) to every apple.                              |
| lighting              | Changes the lighting of the scene.                                               |
| color per apple       | Gives a random (realistic) color to each apple.                                  |
| color per scene       | Generates a random (realistic) color for all apples in the scene.                |
| model per apple       | Chooses a random 3D model for every apple from the set of 3D models shown in Fig. 2. |
| model per scene       | Chooses a random 3D model apple from the set of 3D models shown in Fig. 2 and gives it to all apples in the scene. |

<span style="font-size: smaller; font-style: italic;">List of all augmentations for generating Simulated-Orchards datasets.</span>


## Generating annotations

The technique introduced by this tool enables precise bounding box calculations within Unity 3D.

The algorithm initiates by computing an instance segmentation mask. This process involves utilizing back-projection to derive the directional vector from the camera origin, passing through an x and y-coordinate on the image plane. Within the 3D scene, Unity’s raycasting system casts a ray originating from the camera origin in the direction determined in the prior step. The Unity algorithm locates the first object intersected by the ray, discerning whether it represents the background or an apple. If the intersection corresponds to the background, a value of '0' is inserted into the mask. Conversely, if the intersection corresponds to an apple, an instance identifier specific to that apple is inserted into the map. Bounding boxes are subsequently derived by establishing the minimum and maximum x and y-coordinates for each instance identifier.

<img src="https://github.com/dataset-ninja/simulated-orchards/assets/123257559/8561386d-4c26-421d-9941-ae217ad6c717" alt="image" width="800">

<span style="font-size: smaller; font-style: italic;">Process of calculating bounding boxes. (a) RGB image from the virtual camera. (b) instance segmentation mask created using back-projection. (c) bounding boxes (overlayed on original RGB image) calculated by finding the minimum and maximum x-coordinate and y-coordinate for every instance.</span>

Also, you can check __Mini-Orchards Dataset__ [(available on DatasetNinja)](https://datasetninja.com/mini-orchards) for a more thorough understanding.
