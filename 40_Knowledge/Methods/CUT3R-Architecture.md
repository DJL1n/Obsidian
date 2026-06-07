# CUT3R Architecture

## Goal

Persistent stateful recurrent Transformer for continuous 3D perception: state update + metric-scale pointmap per observation, common-coordinate accumulation.

## Components

- Persistent recurrent state (latent scene representation)
- Image encoder per frame
- State update mechanism: state_{t-1} + image_t → state_t + pointmap_t
- Pointmap regression head: metric-scale in common coordinate
- Support: static/dynamic/varying-length input, virtual unseen view probing

## Related notes

- [[CUT3R]]
- [[Persistent-State-Pointmap]]
- [[Certified-Geometry-Memory]]
