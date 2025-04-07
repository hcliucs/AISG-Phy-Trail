# SLAP: Improving Physical Adversarial Examples with Short-Lived Adversarial Perturbations

Artifacts for the paper "[SLAP: Improving Physical Adversarial Examples with Short-Lived Adversarial Perturbations](https://www.usenix.org/conference/usenixsecurity21/presentation/lovisotto)" published in the [30th Usenix Security Symposium](https://www.usenix.org/conference/usenixsecurity21) (2021).

We introduce a new attack vector to realize physical Adversarial Examples
(AE): using a projector.
By specifically optimizing the perturbation to project on a target object,
we are able to create physical Adversarial Examples that fool deep neural networks
for image classification and object detection.

<p align="center"><img src="https://raw.githubusercontent.com/ssloxford/short-lived-adversarial-perturbations/master/images/projection.gif" width="40%"></p>

We design an optimization pipeline that accounts for (1) the realizability
of projected colours and (2) the robustness of the Adversarial Examples to environmental changes.

<p align="center"><img src="https://raw.githubusercontent.com/ssloxford/short-lived-adversarial-perturbations/master/images/slap.gif" width="60%"></p>

We test four different computer vision models: YOLOv3, MaskRCNN, LISACNN and GTSRBCNN,
our implementations are available in this repository.
SLAP in practice, targeting a Stop Sign:

<p align="center"><img src="https://raw.githubusercontent.com/ssloxford/short-lived-adversarial-perturbations/master/images/videos.gif" width="95%"></p>

The same pipeline can be applied to any target object class, here are examples 
of targeting class "Bottle" and class "Give Way":

![Other objects](images/other_objects.png "Other objects")

See the conference presentation [slides](https://www.usenix.org/system/files/sec21_slides_lovisotto.pdf) and Giulio's conference [presentation video](https://www.youtube.com/watch?v=o4LrVm5LP2E).


## Data and Models
Data and models used in this paper are available in this [release](https://github.com/ssloxford/short-lived-adversarial-perturbations/releases/tag/usenix21).
The `data.zip` folder will contain the following files:
```
.  # data.zip
├── models                    # contains all models
│   ├── yolov3           
│   ├── maskrcnn
│   ├── gtsrbcnn
│   │   │── cvpr18            # taken from cvpr18 paper
│   │   │── cvpr18iyswim      # above with input randomization layer added
│   │   │── usenix21          # architecture from cvpr18 but re-trained
│   │   └── usenix21adv       # above but trained with adversarial loss
│   └── lisacnn               # same subfolders as gtsrbcnn
├── indoor                    # data used in indoors experiment
│   ├── objects               # target objects cutouts
│   ├── profile               # profiling data, used to fit projection model
│   │   │── 120/stop_sign/all_triples.csv  # file used in 120 lux, for stop sign target
│   │   └── ...        
│   └── optimize
│       │── 120/stop_sign/yolov3/_best_projection.png  # projection image used for 120 lux, stop sign, yolov3 model
│       └── ...        
├── outdoor                    # data used in outdoors experiment 
└── datasets                   # image datasets used for re-training
    ├── gtsrb           
    └── lisa
```

## Using this codebase
This repository contains most of the code used in the paper.
We also publish a [**Docker image**](https://hub.docker.com/r/giuliolovisotto/short-lived-adversarial-perturbations/)
containing the execution environment and
[**data and models**](https://github.com/ssloxford/short-lived-adversarial-perturbations/releases/tag/usenix21)
used in this work. 

To use this repository, you will need `docker`, `docker-compose` and a GPU. 
Make sure that the GPU is available within docker containers, (e.g., we had to 
set `"default-runtime": "nvidia"` in `/etc/docker/daemon.json`). 
Most of the code will also work on a CPU once you replace tensorflow-gpu with tensorflow==1.15.3;
if you do not have a GPU you can do this inside the container or rebuild it from a cpu-based image 
(`FROM tensorflow/tensorflow:1.15.3-py3-jupyter`).

Follow these steps:
 
 * `git clone https://github.com/ssloxford/short-lived-adversarial-perturbations.git`
 * `cd short-lived-adversarial-perturbations/`
 * `wget https://github.com/ssloxford/short-lived-adversarial-perturbations/releases/download/usenix21/data.zip`
 the `data.zip` contains models and data necessary to run tests.
 * `unzip data.zip`
 * `docker pull giuliolovisotto/short-lived-adversarial-perturbations:usenix21`
 * `docker-compose up -d`
 * `docker attach slap_container`
 * `nose2` this runs a set of tests which verify that the basic functionalities run correctly.
 
One can check examples of usage by locally opening the notebook
the notebook [example.ipynb](https://localhost:5749)
(the notebook is running inside the container and forwarded on this port).
The password is `slap@usenix21`.

You can also check the content of this notebook online
[example.ipynb](https://github.com/ssloxford/short-lived-adversarial-perturbations/blob/main/code/example.ipynb).

## Citation
If you use this repository please cite the paper as follows:
```
@inproceedings {272218,
  author = {Giulio Lovisotto and Henry Turner and Ivo Sluganovic and Martin Strohmeier and Ivan Martinovic},
  title = {{SLAP}: Improving Physical Adversarial Examples with Short-Lived Adversarial Perturbations},
  booktitle = {30th {USENIX} Security Symposium ({USENIX} Security 21)},
  year = {2021},
  isbn = {978-1-939133-24-3},
  pages = {1865--1882},
  url = {https://www.usenix.org/conference/usenixsecurity21/presentation/lovisotto},
  publisher = {{USENIX} Association},
  month = aug,
}
```

## Contributors
 * [Giulio Lovisotto](https://giuliolovisotto.github.io)
 * [Henry Turner](http://www.cs.ox.ac.uk/people/henry.turner/)

## Acknowledgements

This work was generously supported by a grant from armasuisse and by the Engineering and Physical Sciences Research Council \[grant numbers EP/N509711/1, EP/P00881X/1\].
