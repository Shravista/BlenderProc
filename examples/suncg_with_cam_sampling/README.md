# SUNCG scene with custom camera sampling

![](output-summary.png)

In contrast to the SUNCG basic example, we do here not load precomputed camera poses, but sample them using the `SuncgCameraSampler` module.

## Usage

Execute in the Blender-Pipeline main directory:

```
python run.py examples/suncg_with_cam_sampling/config.yaml <path to house.json> examples/suncg_with_cam_sampling/output
```

* `examples/suncg_with_cam_sampling/config.yaml`: path to the configuration file with pipeline configuration.
* `<path to house.json>`: Path to the house.json file of the SUNCG scene you want to render.
* `examples/suncg_with_cam_sampling/output`: path to the output directory.

## Steps

* Loads a SUNCG scene: `loader.SuncgLoader` module.
* Sample camera positions inside every room: `camera.SuncgCameraSampler` module.
* Automatically adds light sources inside each room: `lighting.SuncgLighting` module.
* Writes sampled camera poses to file: `writer.CameraStateWriter` module.
* Renders normals: `renderer.NormalRenderer` module.
* Renders semantic segmentation map: `renderer.SegMapRenderer` module.
* Renders rgb and depth: `renderer.RgbRenderer` module.
* Merges all into an `.hdf5` file: `writer.Hdf5Writer` module.

## Config file

### SuncgCameraSampler

```yaml
{
  "module": "camera.SuncgCameraSampler",
  "config": {
    "proximity_checks": {
      "min": 1.0
    },
    "min_interest_score": 0.4
  }
},
```

This module goes through all rooms of the loaded house and samples camera poses inside them randomly.

After sampling a pose the pose is only accepted if it is valid according to the properties we have specified:
  * Per default a camera pose is only accepted, if there is no object between it and the floor
  * As we enabled `proximity_checks` with a `min` value of `1.0`, we then only accept the pose if every object in front of it is at least 1 meter away
  * At the end we also check if the sampled view is interesting enough. Therefore a score is calculated based on the number of objects that are visible and how much space they occupy. Only if the score is above `0.4` the pose is accepted.

## Visualization

Visualize the generated data:

```
python scripts/visHdf5Files.py examples/suncg_with_cam_sampling/output/0.hdf5
```

## More examples

* [sung_basic](../suncg_basic): More on rendering SUNCG scenes with fixed camera poses.