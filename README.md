What it does :

A web app streams a live camera feed, runs computer-vision on each frame to detect AprilTags, and periodically logs/plots where detections occur in the image. This produces a “coverage map” so you can see which regions of the field of view reliably return tags and which do not. In practice, the app drops visible markers over time wherever a tag is found, letting you immediately spot blind corners, lighting issues, lens vignetting, or focus problems without doing a full calibration cycle.

How it's built:

cvDetector.py: the vision worker. It opens the camera/stream, performs per-frame detection (e.g., finding tag corners/centroids), and yields structured results for the UI to render. Keeping detection logic in its own module makes it easy to tune thresholds or swap detection algorithms without touching the web UI.

main.py: the web server that wires routes/endpoints to the detector, serves the front end, and exposes a live stream or periodic JSON samples the page can consume. This separation means the app can be run headless on an embedded host while any browser on the network views results.

frontend/templates/ (HTML): the UI layer. Using HTML templates keeps dependencies minimal and lets you iterate on visuals quickly (e.g., marker size/color, timing, or tooltips) without touching the detector.

The repo is roughly half Python and half HTML, reflecting the split between the CV backend and a front end.

Why design it this way:
Fast feedback loop: streaming live detections into a browser avoids the slow “calibrate → check → recalibrate” loop. You can move the grid, adjust lighting, or tweak focus and instantly see whether coverage holes disappear.

Scalability of workflow: the same detector + UI pattern can be repointed at a different camera, lens, or environment without redesigning the tool—useful when standardizing calibrations across a fleet.

End result: a pragmatic, two-tier app (Python CV worker + simple HTML UI) that turns calibration from a black-box batch step into an interactive, real-time process. It lets technicians see coverage, fix issues immediately, and reach reliable, consistent calibrations faster—exactly what you want when preparing video streams for undistortion, reprojection, and downstream AI.
