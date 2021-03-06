# Exercise: Augmented Reality {#exercise-augmented-reality status=draft}

## Skills learned

* Understanding of all the steps in the image pipeline.
* Writing markers on images to aid in debugging.

## Introduction

During the lectures, we have explained one direction of the image pipeline:

    image -> [feature extraction] -> 2D features -> [ground projection] -> 3D world coordinates

In this exercise, we are going to look at the pipeline in the opposite direction.

It is often said that:

> "The inverse of computer vision is computer graphics."

The inverse pipeline looks like this:

    3D world coordinates -> [image projection] -> 2D features -> [rendering] -> image


## Instructions


* Do intrinsics/extrinsics camera calibration of your robot as per the instructions.
* Write the program `dt-augmented-reality` as specified below in [](#exercise-augmented-reality-spec).

Then verify the results in the following 3 situations.


### Calibration pattern

* Put the robot in the middle of the calibration pattern.
* Run the program `dt-augmented-reality` with map file `calibration_pattern.yaml`.

(Adjust the position until you get perfect match of reality and augmented reality.)

### Lane

* Put the robot in the middle of a lane.
* Run the program `dt-augmented-reality` with map file `lane.yaml`.

(Adjust the position until you get perfect match of reality and augmented reality.)

### Intersection

* Put the robot at a stop line at a 4-way intersection in Duckietown.
* Run the program `dt-augmented-reality` with map file `intersection_4way.yaml`.

(Adjust the position until you get perfect match of reality and augmented reality.)

### Submission

Submit the images according to location-specific instructions.


## Specification of `dt-augmented-reality` {#exercise-augmented-reality-spec}

The program is invoked with this syntax:

    $ dt-augmented-reality ![map file]  [![robot name]]

where `![map file]` is a YAML file containing the map (specified in [](#exercise-augmented-reality-map)).

If [![robot name]] is not given, it defaults to the hostname.

The program does the following:

1. It loads the intrinsic / extrinsic calibration parameters for the given robot.
2. It reads the map file.
3. It listens to the image topic `/![robot name]/camera_node/image/compressed`.
4. It reads each image, projects the map features onto the image, and then writes the resulting image to the topic

    /![robot name]/AR/![map file basename]

where `![map file basename]` is the basename of the file without the extension.

## Specification of the map {#exercise-augmented-reality-map}

The map file contains a 3D polygon, defined as a list of points and a list of segments
that join those points.

The format is similar to any data structure, with a few changes:

1. Points are referred to by name.
2. It is possible to specify which reference frame each point is. (This will help make this into
a general tool for debugging various types of problems).

Here is an example of the file contents (hopefully self-explanatory).
This describes 3 points, and two lines.

    points:
        # define three named points: center, left, right
        center: [axle, [0, 0, 0]] # [reference frame, coordinates]
        left: [axle, [0.5, 0.1, 0]]
        right: [axle, [0.5, -0.1, 0]]
    segments:
    - points: [center, left]
      color: [rgb, [1, 0, 0]]
    - points: [center, right]
      color: [rgb, [1, 0, 0]]


### Reference frame specification

The reference frames are defined as follows:

- `axle`: center of the axle; coordinates are 3D.
- `camera`: camera frame; coordinates are 3D.
- `image01`: a reference frame in which 0,0 is top left, and 1,1 is bottom right of the image; coordinates are 2D.

(Other image frames will be introduced later, such as the `world` and `tile` reference frame, which
need the knowledge of the location of the robot.)

### Color specification

RGB colors are written as:

    [rgb, [![R], ![G], ![B]]]

where the RGB values are between 0 and 1.

Moreover, we support the following strings:

- `red` is equivalent to `[rgb, [1,0,0]]`
- `green` is equivalent to `[rgb, [0,1,0]]`
- `blue` is equivalent to `[rgb, [0,0,1]]`
- `yellow` is equivalent to `[rgb, [1,1,0]]`

TODO: add another few colors


## "Map" files


### `hud.yaml`

This pattern serves as a simple test that we can draw lines in image coordinates:

    points:
        TL: [image01, [0, 0]]
        TR: [image01, [0, 1]]
        BR: [image01, [1, 1]]
        BL: [image01, [1, 0]]
    segments:
    - points: [TL, TR]
      color: red
    - points: [TR, BR]
      color: green
    - points: [BR, BL]
      color: blue
    - points: [BL, BR]
      color: yellow

The expected result is to put a border around the image:
red on the top, green on the right, blue on the bottom, yellow on the left.

### `calibration_pattern.yaml`

TODO: to write

### `lane.yaml`

TODO: to write

### `intersection_4way.yaml`


TODO: to write

## Suggestions

Start by using the file `hud.yaml`. To visualize it, you do not need the
calibration data. It will be helpful to make sure that you can do the easy
parts of the exercise: loading the map, and drawing the lines.

## Useful APIs

### Loading a YAML file

To load a YAML file, use the function `yaml_load` from `duckietown_utils`:

    from duckietown_utils import yaml_load

    with open(filename) as f:
        contents = f.read()
        data = yaml_load(contents)


### Reading the calibration data for a robot

TODO: Ask Liam / Andrea for this part

### Path name manipulation

From a file name like `"/path/to/map1.yaml"`, you can obtain the basename without extension `map1` using the following code:

    filename = "/path/to/map1.yaml"
    basename = os.path.basename(filename) # => map1.yaml
    root, ext = os.path.splitext(basename)
    # root = 'map1'
    # ext = '.yaml'

### Drawing primitives

TODO: add here the OpenCV primitives
