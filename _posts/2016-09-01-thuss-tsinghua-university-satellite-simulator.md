---
title: THUSS - Tsinghua University Satellite Simulator
last_modified_at: 2016-09-01T12:00:00+08:00
excerpt_separator: "<!--more-->"
tags:
  - Spaceflight
  - Physical Simulation
categories:
  - Publications
---

**THUSS** : Physical simulation of rendezvous and docking of spacecraft on a desktop. It is one of the smallest 3-DoF satellite simulator in the world.

<!--more-->

### Abstract
Along with progress in space technologies, new space missions, for example, autonomous rendezvous and docking, electrodynamic tether and space debris removal are bringing new challenges to spacecraft system design. To verify the effectiveness of theories and algorithms applied for those new missions, it is necessary to conduct systematic physical experiments on the ground. Thus, a 3 Degree of Freedoms(DOFs) small satellite ground simulation system is designed for this purpose. In this paper, major characteristics and performances of the simulator are summarized. Subsystems' description is given while design efforts to ensure system's upgradability and expandability would be highlighted. Then a major focus is set on the infrared navigation system's implementation. Image processing and recognition procedures are discussed in detail. Eventually, control strategy is illustrated and the system is tested by a series of maneuvers including slewing, position maneuver and trajectory tracking.

### Publications

1. [Implementation of three DoFs small satellite ground simulation system](http://arc.aiaa.org/doi/abs/10.2514/6.2016-0697)
2. [Physical simulation system for satellite](https://docs.google.com/viewer?url=patentimages.storage.googleapis.com/pdfs/660ac1e56223217c2c7a/CN105204373A.pdf)

Citation information:

```bibtex
@Inbook{Yao2016,
  author={Yao, Hongxiang and Wang, Yunjie and Cui, Jie and Bao, Jiali and Yang, Haoguang and Zhu, Zilin and Zheng, Gangtie and Zhu, Zheng Hong},
  chapter={Implementation of three DoFs small satellite ground simulation system},
  series={AIAA SciTech Forum},
  year={2016},
  month={Jan},
  day={02},
  publisher={American Institute of Aeronautics and Astronautics},
  note={0},
  doi={10.2514/6.2016-0697},
  url={http://dx.doi.org/10.2514/6.2016-0697}
}

@misc{2015ThussPatent,
  title={Physical simulation system for satellite},
  url={https://encrypted.google.com/patents/CN105204373A?cl=en},
  year={2015},
  month={Dec},
  day={30},
  publisher={Google Patents},
  note={CN Patent App. CN 201,510,680,932}
}
```

### Open-Source Software

The control system is built with LabVIEW, running on a x86 quad-core celeron on-board computer.

The code archive of the satellite simulator can be found on github:

[History version](https://github.com/HaoguangYang/THUSS)
[Latest version](https://github.com/HaoguangYang/THUSS-Release)
