# Quick Start of Recognition

This tutorial contains 3 parts: Environment Preparation, Image Recognition Experience, and Unknown Category Image Recognition Experience.

If the image category already exists in the image index database, then you can take a reference to chapter [Image Recognition Experience](#image_recognition_experience)，to complete the progress of image recognition；If you wish to recognize unknow category image, which is not included in the index database，you can take a reference to chapter [Unknown Category Image Recognition Experience](#unkonw_category_image_recognition_experience)，to complete the process of creating an index to recognize it。

## Catalogue

* [1. Enviroment Preparation](#enviroment_preperation )
* [2. Image Recognition Experience](#image_recognition_experience)
  * [2.1 Download and Unzip the Inference Model and Demo Data](#download_and_unzip_the_inference_model_and_demo_data)
  * [2.2 Product Recognition and Retrieval](#Product_recognition_and_retrival)
    * [2.2.1 Single Image Recognition](#recognition_of_single_image)
    * [2.2.2 Folder-based Batch Recognition](#folder_based_batch_recognition)
* [3. Unknown Category Image Recognition Experience](#unkonw_category_image_recognition_experience)
  * [3.1 Prepare for the new images and labels](#3.1)
  * [3.2 Build a new Index Library](#build_a_new_index_library)
  * [3.3 Recognize the Unknown Category Images](#Image_differentiation_based_on_the_new_index_library)


<a name="enviroment_preparation"></a>
## 1. Enviroment Preparation

* Installation：Please take a reference to [Quick Installation ](./install_en.md)to configure the PaddleClas environment.

* Using the following command to enter Folder `deploy`. All content and commands in this section need to be run in folder `deploy`.

  ```
  cd deploy
  ```

<a name="image_recognition_experience"></a>
## 2. Image Recognition Experience

The detection model with the recognition inference model for the 4 directions (Logo, Cartoon Face, Vehicle, Product), the address for downloading the test data and the address of the corresponding configuration file are as follows.

| Models Introduction       | Recommended Scenarios  | inference Model  | Predict Config File  | Config File to Build Index Database |
| ------------  | ------------- | -------- | ------- | -------- |
| Generic mainbody detection model | General Scenarios |[Model Download Link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/ppyolov2_r50vd_dcn_mainbody_v1.0_infer.tar) | - | - |
| Logo Recognition Model | Logo Scenario  |  [Model Download Link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/logo_rec_ResNet50_Logo3K_v1.0_infer.tar) | [inference_logo.yaml](../../../deploy/configs/inference_logo.yaml) | [build_logo.yaml](../../../deploy/configs/build_logo.yaml) |
| Cartoon Face Recognition Model| Cartoon Face Scenario  | [Model Download Link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/cartoon_rec_ResNet50_iCartoon_v1.0_infer.tar) | [inference_cartoon.yaml](../../../deploy/configs/inference_cartoon.yaml) | [build_cartoon.yaml](../../../deploy/configs/build_cartoon.yaml) |
| Vehicle Subclassification Model | Vehicle Scenario  |   [Model Download Link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/vehicle_cls_ResNet50_CompCars_v1.0_infer.tar) | [inference_vehicle.yaml](../../../deploy/configs/inference_vehicle.yaml) | [build_vehicle.yaml](../../../deploy/configs/build_vehicle.yaml) |
| Product Recignition Model | Product Scenario  |  [Model Download Link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/product_ResNet50_vd_Inshop_v1.0_infer.tar) | [inference_product.yaml](../../../deploy/configs/inference_product.yaml) | [build_product.yaml](../../../deploy/configs/build_product.yaml) |


Demo data in this tutorial can be downloaded here: [download link](https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/data/recognition_demo_data_v1.0.tar).


**Attention**
1. If you do not have wget installed on Windows, you can download the model by copying the link into your browser and unzipping it in the appropriate folder; for Linux or macOS users, you can right-click and copy the download link to download it via the `wget` command.
2. If you want to install `wget` on macOS, you can run the following command.

```shell
# install homebrew
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)";
# install wget
brew install wget
```

3. If you want to isntall `wget` on Windows, you can refer to [link](https://www.cnblogs.com/jeshy/p/10518062.html). If you want to install `tar` on Windows, you can refer to [link](https://www.cnblogs.com/chooperman/p/14190107.html).


* You can download and unzip the data and models by following the command below

```shell
mkdir models
cd models
# Download and unzip the inference model
wget {Models download link} && tar -xf {Name of the tar archive}
cd ..

# Download the demo data and unzip
wget {Data download link} && tar -xf {Name of the tar archive}
```


<a name="download_and_unzip_the_inference_model_and_demo_data"></a>
### 2.1 Download and Unzip the Inference Model and Demo Data

Take the product recognition as an example, download the detection model, recognition model and product recognition demo data with the following commands.

```shell
mkdir models
cd models
# Download the generic detection inference model and unzip it
wget https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/ppyolov2_r50vd_dcn_mainbody_v1.0_infer.tar && tar -xf ppyolov2_r50vd_dcn_mainbody_v1.0_infer.tar
# Download and unpack the inference model
wget https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/models/inference/product_ResNet50_vd_aliproduct_v1.0_infer.tar && tar -xf product_ResNet50_vd_aliproduct_v1.0_infer.tar
cd ..

# Download the demo data and unzip it
wget https://paddle-imagenet-models-name.bj.bcebos.com/dygraph/rec/data/recognition_demo_data_v1.0.tar && tar -xf recognition_demo_data_v1.0.tar
```

Once unpacked, the `recognition_demo_data_v1.0` folder should have the following file structure.

```
├── recognition_demo_data_v1.0
│   ├── gallery_cartoon
│   ├── gallery_logo
│   ├── gallery_product
│   ├── gallery_vehicle
│   ├── test_cartoon
│   ├── test_logo
│   ├── test_product
│   └── test_vehicle
├── ...
```

here, original images to build index are in folder `gallery_xxx`, test images are in folder `test_xxx`. You can also access specific folder for more details.

The `models` folder should have the following file structure.

```
├── product_ResNet50_vd_aliproduct_v1.0_infer
│   ├── inference.pdiparams
│   ├── inference.pdiparams.info
│   └── inference.pdmodel
├── ppyolov2_r50vd_dcn_mainbody_v1.0_infer
│   ├── inference.pdiparams
│   ├── inference.pdiparams.info
│   └── inference.pdmodel
```

<a name="Product_recognition_and_retrival"></a>
### 2.2 Product Recognition and Retrieval

Take the product recognition demo as an example to show the recognition and retrieval process (if you wish to try other scenarios of recognition and retrieval, replace the corresponding configuration file after downloading and unzipping the corresponding demo data and model to complete the prediction)。


<a name="recognition_of_single_image"></a>
#### 2.2.1 Single Image Recognition

Run the following command to identify and retrieve the image `./recognition_demo_data_v1.0/test_product/daoxiangcunjinzhubing_6.jpg` for recognition and retrieval

```shell
# use the following command to predict using GPU.
python3.7 python/predict_system.py -c configs/inference_product.yaml
# use the following command to predict using CPU
python3.7 python/predict_system.py -c configs/inference_product.yaml
```

**Note:** Program lib used to build index is compliled on our machine, if error occured because of the environment, you can refer to [vector search tutorial](../../../deploy/vector_search/README.md) to rebuild the lib.


The image to be retrieved is shown below.

<div align="center">
<img src="../../images/recognition/product_demo/query/daoxiangcunjinzhubing_6.jpg"  width = "400" />
</div>


The final output is shown below.

```
[{'bbox': [287, 129, 497, 326], 'rec_docs': '稻香村金猪饼', 'rec_scores': 0.8309420943260193}, {'bbox': [99, 242, 313, 426], 'rec_docs': '稻香村金猪饼', 'rec_scores': 0.7245652079582214}]
```


where bbox indicates the location of the detected object, rec_docs indicates the labels corresponding to the label in the index dabase that are most similar to the detected object, and rec_scores indicates the corresponding confidence.


The detection result is also saved in the folder `output`, for this image, the visualization result is as follows.

<div align="center">
<img src="../../images/recognition/product_demo/result/daoxiangcunjinzhubing_6.jpg"  width = "400" />
</div>


<a name="folder_based_batch_recognition"></a>
#### 2.2.2 Folder-based Batch Recognition

If you want to predict the images in the folder, you can directly modify the `Global.infer_imgs` field in the configuration file, or you can also modify the corresponding configuration through the following `-o` parameter.

```shell
# using the following command to predict using GPU, you can append `-o Global.use_gpu=False` to predict using CPU.
python3.7 python/predict_system.py -c configs/inference_product.yaml -o Global.infer_imgs="./recognition_demo_data_v1.0/test_product/"
```


The results on the screen are shown as following.

```
...
[{'bbox': [37, 29, 123, 89], 'rec_docs': '香奈儿包', 'rec_scores': 0.6163763999938965}, {'bbox': [153, 96, 235, 175], 'rec_docs': '香奈儿包', 'rec_scores': 0.5279821157455444}]
[{'bbox': [735, 562, 1133, 851], 'rec_docs': '香奈儿包', 'rec_scores': 0.5588355660438538}]
[{'bbox': [124, 50, 230, 129], 'rec_docs': '香奈儿包', 'rec_scores': 0.6980369687080383}]
[{'bbox': [0, 0, 275, 183], 'rec_docs': '香奈儿包', 'rec_scores': 0.5818190574645996}]
[{'bbox': [400, 1179, 905, 1537], 'rec_docs': '香奈儿包', 'rec_scores': 0.9814301133155823}]
[{'bbox': [544, 4, 1482, 932], 'rec_docs': '香奈儿包', 'rec_scores': 0.5143815279006958}]
[{'bbox': [29, 42, 194, 183], 'rec_docs': '香奈儿包', 'rec_scores': 0.9543638229370117}]
...
```

All the visualization results are also saved in folder `output`.


Furthermore, the recognition inference model path can be changed by modifying the `Global.rec_inference_model_dir` field, and the path of the index to the index databass can be changed by modifying the `IndexProcess.index_path` field.


<a name="unkonw_category_image_recognition_experience"></a>
## 3. Recognize Images of Unknown Category

To recognize the image `./recognition_demo_data_v1.0/test_product/anmuxi.jpg`, run the command as follows:

```shell
python3.7 python/predict_system.py -c configs/inference_product.yaml -o Global.infer_imgs="./recognition_demo_data_v1.0/test_product/anmuxi.jpg"
```

The image to be retrieved is shown below.

<div align="center">
<img src="../../images/recognition/product_demo/query/anmuxi.jpg"  width = "400" />
</div>

The output is empty.

Since the index infomation is not included in the corresponding index databse, the recognition result is empty or not proper. At this time, we can complete the image recognition of unknown categories by constructing a new index database.

When the index database cannot cover the scenes we actually recognise, i.e. when predicting images of unknown categories, we need to add similar images of the corresponding categories to the index databasey, thus completing the recognition of images of unknown categories ，which does not require retraining.

<a name="3.1"></a>
### 3.1 Prepare for the new images and labels

First, you need to copy the images which are similar with the image to retrieval to the original images for the index database. The command is as follows.

```shell
cp -r  ../docs/images/recognition/product_demo/gallery/anmuxi ./recognition_demo_data_v1.0/gallery_product/gallery/
```

Then you need to create a new label file which records the image path and label information. Use the following command to create a new file based on the original one.

```shell
# copy the file
cp recognition_demo_data_v1.0/gallery_product/data_file.txt recognition_demo_data_v1.0/gallery_product/data_file_update.txt
```

Then add some new lines into the new label file, which is shown as follows.

```
gallery/anmuxi/001.jpg	安慕希酸奶
gallery/anmuxi/002.jpg	安慕希酸奶
gallery/anmuxi/003.jpg	安慕希酸奶
gallery/anmuxi/004.jpg	安慕希酸奶
gallery/anmuxi/005.jpg	安慕希酸奶
gallery/anmuxi/006.jpg	安慕希酸奶
```

Each line can be splited into two fields. The first field denotes the relative image path, and the second field denotes its label. The `delimiter` is `tab` here.


<a name="build_a_new_index_library"></a>
### 3.2 Build a new Index Base Library

Use the following command to build the index to accelerate the retrieval process after recognition.

```shell
python3.7 python/build_gallery.py -c configs/build_product.yaml -o IndexProcess.data_file="./recognition_demo_data_v1.0/gallery_product/data_file_update.txt" -o IndexProcess.index_path="./recognition_demo_data_v1.0/gallery_product/index_update"
```

Finally, the new index information is stored in the folder`./recognition_demo_data_v1.0/gallery_product/index_update`. Use the new index database for the above index.


<a name="Image_differentiation_based_on_the_new_index_library"></a>
### 3.2 Recognize the Unknown Category Images

To recognize the image `./recognition_demo_data_v1.0/test_product/anmuxi.jpg`, run the command as follows.

```shell
# using the following command to predict using GPU, you can append `-o Global.use_gpu=False` to predict using CPU.
python3.7 python/predict_system.py -c configs/inference_product.yaml -o Global.infer_imgs="./recognition_demo_data_v1.0/test_product/anmuxi.jpg" -o IndexProcess.index_path="./recognition_demo_data_v1.0/gallery_product/index_update"
```

The output is as follows:

```
[{'bbox': [243, 80, 523, 522], 'rec_docs': '安慕希酸奶', 'rec_scores': 0.5570770502090454}]
```

The final recognition result is `安慕希酸奶`, which is corrrect, the visualization result is as follows.

<div align="center">
<img src="../../images/recognition/product_demo/result/anmuxi.jpg"  width = "400" />
</div>
