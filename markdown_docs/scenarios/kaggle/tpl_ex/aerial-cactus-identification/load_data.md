## FunctionDef load_test_images(folder)
**load_test_images**: This function loads test images from a specified folder and returns them as numpy arrays along with their filenames.

parameters:
· folder: A string representing the path to the directory containing the test images.

Code Description: The function iterates over each file in the provided folder using `os.listdir(folder)`. For each file, it opens the image using `Image.open(os.path.join(folder, filename))` from the PIL library. If the image is successfully loaded (i.e., not None), it converts the image to a numpy array using `np.array(img)` and appends this array to the `images` list. Simultaneously, it appends the original filename to the `filenames` list. After processing all files in the directory, the function returns two values: a numpy array containing all images and a list of filenames corresponding to these images.

Note: This function is typically used in machine learning workflows where test data needs to be loaded for inference or evaluation purposes. It assumes that all files in the specified folder are valid image files that can be opened by PIL's Image.open method.

Output Example: If the folder contains two images named 'image1.tif' and 'image2.tif', the function might return something like this:

```
(
    array([[[[255, 0, 0],
             [0, 255, 0]],
            [[0, 0, 255],
             [255, 255, 255]]],

           [[[128, 128, 128],
             [64, 64, 64]],
            [[32, 32, 32],
             [192, 192, 192]]]], dtype=uint8),

    ['image1.tif', 'image2.tif']
)
```

This output consists of a numpy array with shape (number_of_images, height, width, channels) and a list of filenames.
## FunctionDef load_images_and_labels(csv_file, image_folder)
**load_images_and_labels**: This function loads images and their corresponding labels from a specified CSV file and image folder. It reads the CSV to extract image identifiers and labels, then opens each image using its identifier, converts it into a NumPy array, and stores both the image data and label in separate lists which are finally converted to NumPy arrays before being returned.

**parameters**:
· csv_file: A string representing the path to the CSV file containing image identifiers and their corresponding labels. The CSV should have at least two columns: 'id' for the image filename and 'has_cactus' for the label indicating whether an aerial cactus is present in the image.
· image_folder: A string representing the directory where the images are stored. Each image's filename must match the 'id' specified in the CSV file.

**Code Description**: The function starts by initializing two empty lists, `images` and `labels`, to store the image data and their corresponding labels respectively. It then reads the provided CSV file into a pandas DataFrame using `pd.read_csv(csv_file)`. For each row in the DataFrame, it constructs the full path to the image by joining the `image_folder` with the image identifier from the 'id' column. The image is opened using `Image.open()` and converted to a NumPy array if successfully loaded. This array is appended to the `images` list, while the label (value of 'has_cactus') is added to the `labels` list. After all images have been processed, both lists are converted into NumPy arrays for efficient handling and returned as a tuple.

**Note**: The function assumes that all image files exist in the specified directory and can be opened without errors. It also assumes that the CSV file is correctly formatted with 'id' and 'has_cactus' columns. If an image cannot be loaded, it will be skipped (though this scenario is not explicitly handled in the code).

**Output Example**: The function returns a tuple of two NumPy arrays. The first array contains the pixel data for each image, while the second array contains the labels corresponding to these images.

For example:
```
(array([[[[207, 194, 203],
          [208, 195, 204],
          ...,
          [191, 183, 164],
          [176, 168, 149],
          [181, 173, 152]]]], dtype=uint8),
 array([1, 0, 1, 0, 1, 1], dtype=int64))
```
In this example, the first NumPy array represents a single image's pixel data, and the second array contains labels for multiple images.
## FunctionDef load_from_raw_data
**load_from_raw_data**: This function loads raw image data from disk into a uniform format suitable for machine learning processing. It reads training images along with their labels, as well as test images without labels, and returns these datasets alongside the identifiers of the test images.

parameters:
· None: The function does not take any parameters directly; it operates on predefined file paths within its implementation.

Code Description: The function begins by calling `load_images_and_labels` to load training data. This involves reading a CSV file that contains image identifiers and their corresponding labels, then loading each image from the specified folder into a NumPy array format. These arrays are stored in `X`, while the labels are stored in `y`.

Next, the function loads test images by invoking `load_test_images`. It specifies the directory containing the test images, and this function returns both the images as NumPy arrays and their filenames. The function then extracts the identifiers from these filenames by removing the file extension (".tif") and stores them in `test_ids`.

The final step is to return a tuple containing four elements: `X` (training images), `y` (training labels), `X_test` (test images), and `test_ids` (identifiers for test images).

Note: This function assumes that the training CSV file and image directories are correctly formatted and accessible at the specified paths. It is crucial for these files to be present and properly structured to ensure successful data loading.

Output Example: The output of this function might look like the following:

```
(
    array([[[[207, 194, 203],
             [208, 195, 204],
             ...,
             [191, 183, 164],
             [176, 168, 149],
             [181, 173, 152]]]], dtype=uint8),
    array([1, 0, 1, 0, 1, 1], dtype=int64),
    array([[[[255, 0, 0],
              [0, 255, 0]],
             [[0, 0, 255],
              [255, 255, 255]]],

            [[[128, 128, 128],
              [64, 64, 64]],
             [[32, 32, 32],
              [192, 192, 192]]]], dtype=uint8),
    ['image1', 'image2']
)
```

In this example:
- The first NumPy array represents the pixel data for training images.
- The second NumPy array contains labels corresponding to these training images.
- The third NumPy array represents the pixel data for test images.
- The list of strings contains identifiers for each test image, which are used later in generating submission files.
