![Capture](https://user-images.githubusercontent.com/96216518/171773987-05a6b423-df46-44d8-a788-61d3cec19b68.PNG)
// Adjust size of detected image and render it on-screen
  public void draw(
      float[] viewMatrix,
      float[] projectionMatrix,
      AugmentedImage augmentedImage,
      Anchor centerAnchor,
      float[] colorCorrectionRgba) {
    float[] tintColor =
        convertHexToColor(TINT_COLORS_HEX[augmentedImage.getIndex() % TINT_COLORS_HEX.length]);

    final float mazeEdgeSize = 492.65f; // Magic number of maze size
    final float maxImageEdgeSize = Math.max(augmentedImage.getExtentX(), augmentedImage.getExtentZ()); // Get largest detected image edge size

    Pose anchorPose = centerAnchor.getPose();

    float mazeScaleFactor = maxImageEdgeSize / mazeEdgeSize; // scale to set Maze to image size
    float[] modelMatrix = new float[16];

    // OpenGL Matrix operation is in the order: Scale, rotation and Translation
    // So the manual adjustment is after scale
    // The 251.3f and 129.0f is magic number from the maze obj file
    // You mustWe need to do this adjustment because the maze obj file
    // is not centered around origin. Normally when you
    // work with your own model, you don't have this problem.
    Pose mazeModelLocalOffset = Pose.makeTranslation(
                                -251.3f * mazeScaleFactor,
                                0.0f,
                                129.0f * mazeScaleFactor);
    anchorPose.compose(mazeModelLocalOffset).toMatrix(modelMatrix, 0);
    mazeRenderer.updateModelMatrix(modelMatrix, mazeScaleFactor, mazeScaleFactor/10.0f, mazeScaleFactor); // This line relies on a change in ObjectRenderer.updateModelMatrix later in this codelab.
    mazeRenderer.draw(viewMatrix, projectionMatrix, colorCorrectionRgba, tintColor);
  }
  // Scale X, Y, Z coordinates unevenly
public void updateModelMatrix(float[] modelMatrix, float scaleFactorX, float scaleFactorY, float scaleFactorZ) {
    float[] scaleMatrix = new float[16];
    Matrix.setIdentityM(scaleMatrix, 0);
    scaleMatrix[0] = scaleFactorX;
    scaleMatrix[5] = scaleFactorY;
    scaleMatrix[10] = scaleFactorZ;
    Matrix.multiplyMM(this.modelMatrix, 0, modelMatrix, 0, scaleMatrix, 0);
