# Mobilenet-
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "638b664b",
   "metadata": {},
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import tensorflow as tf\n",
    "from tensorflow import keras\n",
    "from tensorflow.keras.layers import Dense, Activation\n",
    "from tensorflow.keras.optimizers import Adam\n",
    "from tensorflow.keras.metrics import categorical_crossentropy\n",
    "from tensorflow.keras.preprocessing.image import ImageDataGenerator\n",
    "from tensorflow.keras.preprocessing import image\n",
    "from tensorflow.keras.models import Model\n",
    "from tensorflow.keras.applications import imagenet_utils\n",
    "#from sklearn.metrics import confusion_matrix\n",
    "import itertools\n",
    "import os\n",
    "import shutil\n",
    "import random\n",
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "64cebe0f",
   "metadata": {},
   "outputs": [],
   "source": [
    "import pathlib "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "bc8379c5",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "'C:\\\\Users\\\\vasu0\\\\Desktop\\\\lenovo data(descktop)\\\\MLLL\\\\Projects\\\\dogcat_new'"
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "os.getcwd()"
   ]
  },
  {
   "cell_type": "raw",
   "id": "fa0fae7d",
   "metadata": {},
   "source": [
    "from pathlib import Path\n",
    "path = Path.cwd() / 'mobilenet_samples'\n",
    "path.mkdir()"
   ]
  },
  {
   "cell_type": "raw",
   "id": "f01036b3",
   "metadata": {},
   "source": [
    "p= path.cwd()\n",
    "p"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "id": "5d5c821c",
   "metadata": {},
   "outputs": [],
   "source": [
    "mobile = tf.keras.applications.mobilenet.MobileNet()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "cb8464e0",
   "metadata": {},
   "outputs": [],
   "source": [
    "def prepare_image(file):\n",
    "    img_path = \"C:\\\\Users\\\\vasu0\\\\Desktop\\\\lenovo data(descktop)\\\\MLLL\\\\Projects\\\\dogcat_new\\\\mobilenet_samples\\\\pedastrian.1.png\"\n",
    "    img = image.load_img(img_path, target_size=(224, 224))\n",
    "    img_array = image.img_to_array(img)\n",
    "    img_array_expanded_dims = np.expand_dims(img_array, axis=0)\n",
    "    return tf.keras.applications.mobilenet.preprocess_input(img_array_expanded_dims)"
   ]
  },
  {
   "cell_type": "raw",
   "id": "26f59b43",
   "metadata": {},
   "source": [
    "\n",
    "img_path = \"C:\\\\Users\\\\vasu0\\\\Desktop\\\\lenovo data(descktop)\\\\MLLL\\\\Projects\\\\dogcat_new\\\\mobilenet_samples\\\\dog.1.png\"\n",
    "img = image.load_img(img_path, target_size=(224, 224))\n",
    "img_array = image.img_to_array(img)\n",
    "img_array_expanded_dims = np.expand_dims(img_array, axis=0)\n",
    "tf.keras.applications.mobilenet.preprocess_input(img_array_expanded_dims)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "id": "06c97f26",
   "metadata": {},
   "outputs": [],
   "source": [
    "from IPython.display import display\n",
    "from PIL import Image"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "id": "5a8892be",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<IPython.core.display.Image object>"
      ]
     },
     "execution_count": 15,
     "metadata": {
      "image/png": {
       "height": 200,
       "width": 300
      }
     },
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from IPython.display import Image\n",
    "Image(filename=\"C:\\\\Users\\\\vasu0\\\\Desktop\\\\lenovo data(descktop)\\\\MLLL\\\\Projects\\\\dogcat_new\\\\mobilenet_samples\\\\pedastrian.1.png\", width=300,height=200) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "id": "1f1ec33e",
   "metadata": {},
   "outputs": [],
   "source": [
    "preprocessed_image = prepare_image('pedastrian.1.png')\n",
    "predictions = mobile.predict(preprocessed_image)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "id": "94044088",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[8.88373552e-09, 6.60704018e-07, 4.85394080e-09, 3.33991990e-09,\n",
       "        6.87645541e-09, 2.23951872e-07, 6.57308661e-08, 2.64213827e-06,\n",
       "        8.38538654e-07, 3.31083250e-09, 7.45268551e-07, 3.19592829e-07,\n",
       "        9.82446636e-07, 1.09042531e-09, 1.15800624e-07, 5.16747878e-06,\n",
       "        3.20888512e-06, 5.05532853e-06, 1.98960990e-07, 4.57021905e-08,\n",
       "        4.29984492e-09, 2.62689409e-05, 1.37323095e-05, 4.70704236e-07,\n",
       "        6.42952655e-05, 1.15687231e-04, 1.16054969e-06, 2.41957205e-05,\n",
       "        9.37158575e-07, 2.10737340e-07, 1.70041267e-06, 1.66291022e-04,\n",
       "        6.59543875e-05, 5.57085080e-08, 1.46200165e-07, 1.84886506e-08,\n",
       "        3.23821150e-05, 2.07420999e-06, 2.91285796e-05, 3.99535274e-05,\n",
       "        1.88009071e-05, 3.23789322e-07, 8.56876886e-06, 9.95765095e-06,\n",
       "        4.21851473e-06, 7.19596665e-06, 5.06040760e-06, 3.22826423e-07,\n",
       "        1.22718660e-07, 1.18957928e-06, 2.42228839e-06, 1.17227799e-07,\n",
       "        5.08214661e-08, 6.54593446e-09, 1.03742792e-08, 4.02040072e-08,\n",
       "        6.72288297e-06, 4.24334123e-10, 1.25361783e-08, 4.52976735e-07,\n",
       "        3.91277297e-07, 2.89503514e-08, 6.48707328e-08, 3.62827507e-07,\n",
       "        1.40122779e-07, 3.40593687e-09, 1.66776132e-07, 8.86105056e-07,\n",
       "        1.48245442e-06, 1.25948077e-07, 1.23989450e-08, 5.23991512e-05,\n",
       "        6.15329118e-05, 4.04074090e-05, 2.52069162e-06, 2.17421348e-06,\n",
       "        2.31345530e-05, 9.28309589e-07, 4.38635034e-05, 1.67466078e-05,\n",
       "        6.39397157e-08, 4.44820728e-07, 3.81626023e-06, 1.68488373e-06,\n",
       "        3.44188251e-08, 2.99202566e-06, 2.12337525e-07, 4.73089267e-06,\n",
       "        6.15653926e-06, 6.30074055e-08, 3.23116240e-07, 1.78529220e-07,\n",
       "        2.42057681e-08, 1.51329141e-06, 1.41956079e-05, 5.76046055e-09,\n",
       "        7.23972789e-07, 2.93664026e-08, 3.46463724e-09, 3.58413700e-07,\n",
       "        3.92986870e-08, 5.37445199e-09, 1.72204284e-09, 2.43587351e-07,\n",
       "        9.54585175e-08, 1.76993154e-07, 7.15822868e-09, 1.10928357e-10,\n",
       "        5.15125897e-09, 3.43874582e-08, 1.91634030e-07, 9.27328756e-08,\n",
       "        2.07584748e-07, 2.89535257e-10, 2.15171934e-08, 1.10738050e-07,\n",
       "        7.17854620e-09, 1.92561913e-07, 1.85938163e-07, 3.26115696e-07,\n",
       "        2.04945337e-07, 1.72451491e-05, 1.64021112e-04, 2.02733536e-05,\n",
       "        3.50424921e-06, 7.01444947e-07, 2.92456366e-06, 5.34868207e-07,\n",
       "        5.10199186e-07, 4.76327727e-07, 1.07992634e-07, 1.68988268e-09,\n",
       "        5.15007947e-09, 6.13462259e-09, 6.63534783e-09, 2.52488377e-07,\n",
       "        4.53992641e-07, 1.87255614e-10, 6.74371003e-09, 4.89274782e-07,\n",
       "        1.52918659e-07, 3.61374532e-06, 2.19028425e-06, 5.40890369e-07,\n",
       "        1.96931573e-08, 6.43355875e-08, 7.54658780e-10, 2.90926110e-08,\n",
       "        8.15980030e-08, 4.11344869e-09, 8.68634004e-07, 1.16960236e-05,\n",
       "        2.34264164e-07, 2.27827712e-07, 1.52927555e-07, 3.88204035e-06,\n",
       "        4.98122859e-07, 4.44970738e-06, 2.13815724e-06, 1.25928381e-07,\n",
       "        1.15653017e-06, 2.31223325e-07, 8.11983341e-07, 1.01830392e-06,\n",
       "        4.54942267e-07, 2.28528529e-06, 1.00919294e-06, 1.08334476e-07,\n",
       "        3.71472765e-07, 3.05874522e-07, 2.56864382e-06, 3.32850817e-07,\n",
       "        6.82543231e-08, 1.98083541e-08, 4.66979024e-08, 1.00855027e-06,\n",
       "        7.54773737e-07, 3.66215340e-06, 1.80102319e-07, 1.22476217e-06,\n",
       "        1.90485011e-07, 9.46183832e-07, 7.28153964e-08, 1.36266851e-06,\n",
       "        2.27549641e-08, 1.77454396e-07, 1.42722422e-06, 4.52705308e-06,\n",
       "        2.40229298e-07, 1.06867603e-07, 2.21767982e-06, 1.71972817e-06,\n",
       "        2.50084149e-08, 8.35070040e-08, 4.63051677e-08, 2.22323324e-05,\n",
       "        1.82331792e-06, 7.18182991e-06, 1.45848333e-06, 7.35674007e-07,\n",
       "        1.29986927e-06, 2.30458753e-07, 2.74586853e-07, 2.39681697e-07,\n",
       "        1.25013287e-06, 3.68015407e-07, 1.82848353e-07, 1.30582876e-07,\n",
       "        3.64166084e-08, 4.10405976e-09, 2.93111452e-06, 3.58255670e-06,\n",
       "        2.96573307e-06, 5.51038738e-06, 3.33211415e-06, 7.90373542e-06,\n",
       "        5.92350418e-08, 4.04233646e-07, 9.96241951e-08, 1.45570326e-07,\n",
       "        7.47537115e-07, 6.50346010e-07, 4.47215484e-07, 2.06977211e-06,\n",
       "        8.20058858e-07, 3.65357351e-07, 2.61051741e-06, 1.37996167e-07,\n",
       "        2.00112922e-08, 3.97062053e-07, 6.57350085e-08, 1.07313802e-07,\n",
       "        7.26024211e-07, 1.00602279e-06, 9.12750764e-08, 8.68518043e-07,\n",
       "        3.98519973e-07, 6.18118383e-06, 2.33743080e-07, 3.89106361e-07,\n",
       "        7.89638591e-07, 2.78054858e-06, 8.98516745e-08, 3.20133537e-08,\n",
       "        6.35241406e-07, 4.83318490e-06, 3.02214218e-08, 2.24133387e-08,\n",
       "        5.98272933e-08, 4.88031695e-08, 2.08143280e-08, 4.64315099e-06,\n",
       "        5.64536492e-07, 4.01931217e-08, 2.94463152e-05, 3.27178759e-06,\n",
       "        3.03199982e-07, 1.20444739e-07, 3.77669664e-07, 6.68742871e-07,\n",
       "        1.83277564e-06, 1.51000521e-07, 3.46273482e-05, 5.26358193e-08,\n",
       "        2.42489904e-07, 2.52292853e-06, 7.79984077e-08, 4.14435277e-08,\n",
       "        3.25567214e-07, 5.57828628e-07, 1.04544242e-07, 5.71033922e-08,\n",
       "        2.37508686e-07, 2.99488434e-09, 5.63993519e-09, 5.40110179e-08,\n",
       "        1.14737190e-07, 6.33310947e-06, 4.06263825e-05, 3.04938709e-07,\n",
       "        3.33239723e-06, 9.01959138e-05, 8.19254183e-06, 8.88632883e-07,\n",
       "        8.52429184e-06, 4.35583843e-06, 8.43904058e-09, 1.19638225e-05,\n",
       "        2.67121237e-07, 2.65191513e-08, 5.59718217e-07, 3.42729862e-07,\n",
       "        7.28941950e-06, 1.35976652e-05, 1.15315082e-07, 7.19772641e-09,\n",
       "        5.94983085e-08, 4.42509034e-08, 2.10960138e-07, 8.82319739e-07,\n",
       "        6.99865325e-07, 2.72016312e-08, 1.19665274e-05, 1.29978935e-05,\n",
       "        1.63140669e-07, 8.18184809e-09, 3.20400289e-07, 1.37319816e-07,\n",
       "        3.45166177e-06, 1.98900011e-06, 6.08014147e-07, 2.30624773e-05,\n",
       "        2.05092465e-06, 4.80580979e-08, 2.89183721e-04, 2.72067555e-05,\n",
       "        3.15952161e-06, 2.98834839e-05, 2.02152864e-06, 6.86251860e-06,\n",
       "        1.62527374e-06, 2.54919132e-06, 1.79116660e-06, 9.08217316e-06,\n",
       "        5.37244705e-06, 3.71640567e-06, 1.76888043e-05, 5.76290904e-06,\n",
       "        2.15673737e-07, 4.12075352e-08, 1.05765741e-06, 2.12002035e-07,\n",
       "        1.70804952e-08, 2.97378932e-07, 1.04343343e-07, 3.52108401e-07,\n",
       "        7.82579605e-07, 1.99557985e-06, 2.79938672e-06, 1.52770724e-07,\n",
       "        2.60751426e-06, 2.59350827e-07, 1.80319034e-07, 1.82909744e-07,\n",
       "        1.18976246e-07, 1.50463677e-08, 2.29961046e-07, 7.65275399e-09,\n",
       "        1.01798534e-08, 7.00278576e-07, 2.29715678e-08, 1.43166554e-08,\n",
       "        2.03575414e-08, 9.26770497e-07, 7.50438147e-08, 4.17526236e-09,\n",
       "        1.88998956e-06, 6.76225852e-07, 1.52328380e-06, 1.14854338e-05,\n",
       "        4.50841480e-06, 1.48569268e-06, 6.06093590e-06, 7.50143784e-07,\n",
       "        2.74938111e-06, 7.30173851e-05, 2.64505888e-05, 1.09709281e-05,\n",
       "        1.64223729e-05, 2.13877647e-05, 8.67887522e-08, 1.13812007e-06,\n",
       "        5.81172856e-07, 1.35000728e-05, 4.15813593e-05, 4.17798098e-08,\n",
       "        5.48478340e-07, 1.52313461e-07, 2.69403677e-06, 1.84356555e-04,\n",
       "        3.84785750e-08, 4.71915628e-05, 1.44924365e-06, 1.29701693e-05,\n",
       "        2.41205544e-06, 1.43618912e-08, 4.26963931e-09, 2.46960667e-06,\n",
       "        1.08315010e-06, 2.08968984e-07, 3.09604009e-09, 4.77876938e-09,\n",
       "        1.97852941e-07, 7.20639491e-07, 1.37191201e-08, 3.31855210e-09,\n",
       "        1.49704385e-06, 6.24492600e-08, 2.13577300e-06, 1.56108499e-07,\n",
       "        3.37040547e-06, 1.30303181e-06, 7.45919579e-06, 2.68274707e-05,\n",
       "        6.88235101e-04, 1.12547423e-06, 1.59062358e-04, 8.34993989e-05,\n",
       "        4.14310399e-08, 1.79018408e-01, 3.33929719e-08, 6.71216810e-07,\n",
       "        4.12271083e-06, 3.02482858e-05, 1.32122295e-05, 5.32214972e-06,\n",
       "        4.87963640e-04, 1.97351028e-06, 1.94443292e-05, 1.77427683e-05,\n",
       "        4.99537634e-07, 1.72359319e-06, 4.20687184e-06, 2.27769931e-07,\n",
       "        2.93774679e-06, 3.43709530e-07, 1.45921167e-05, 9.85676323e-08,\n",
       "        9.38758831e-07, 1.09656617e-06, 5.68171845e-06, 1.02207537e-07,\n",
       "        2.66374468e-06, 1.78946777e-06, 3.97701206e-06, 1.81713058e-05,\n",
       "        4.81776858e-08, 6.91159698e-07, 7.60162993e-06, 1.22975825e-05,\n",
       "        2.00444447e-05, 1.21845214e-05, 3.23229062e-04, 2.30874539e-05,\n",
       "        4.47383809e-06, 8.00713588e-06, 2.56985277e-05, 2.70876662e-05,\n",
       "        2.61341338e-03, 2.97671896e-08, 2.40898553e-05, 4.26092856e-06,\n",
       "        5.16177295e-07, 4.01801884e-07, 1.17293837e-06, 2.10501435e-06,\n",
       "        1.96874207e-05, 3.38690443e-05, 1.48395528e-04, 3.16998232e-08,\n",
       "        7.40268913e-09, 3.34260153e-06, 2.81282209e-05, 6.32098454e-05,\n",
       "        4.29349575e-06, 1.55771502e-07, 8.98824419e-06, 5.67219729e-07,\n",
       "        9.66458720e-06, 7.81387843e-09, 2.50177563e-05, 1.62448259e-07,\n",
       "        1.10957774e-06, 1.62757951e-05, 1.34263971e-07, 4.04094136e-09,\n",
       "        1.00176112e-05, 2.14673069e-04, 1.15668072e-05, 1.08638596e-06,\n",
       "        6.28277576e-06, 4.37057060e-05, 2.06700534e-07, 3.67570664e-07,\n",
       "        7.44873114e-06, 2.69634999e-08, 9.86716145e-07, 6.35235992e-05,\n",
       "        2.97152146e-05, 2.50661537e-06, 1.23560321e-05, 5.22099086e-04,\n",
       "        2.70382643e-05, 4.80093377e-07, 4.43943962e-03, 1.97349053e-07,\n",
       "        1.38257732e-04, 7.43053988e-06, 1.24951440e-03, 2.09891132e-05,\n",
       "        2.88053115e-09, 5.69181611e-06, 1.08496706e-05, 1.83587460e-07,\n",
       "        4.65757739e-06, 4.09262384e-07, 1.00653432e-08, 8.14955092e-06,\n",
       "        8.64302001e-06, 8.40028935e-09, 1.11714087e-06, 5.73755727e-08,\n",
       "        2.94584497e-05, 8.93783678e-08, 2.45077354e-06, 1.91577652e-08,\n",
       "        9.78220396e-06, 1.43440229e-05, 2.80946806e-05, 1.22520055e-06,\n",
       "        1.19133951e-07, 1.97876716e-07, 2.77127265e-06, 2.22788984e-07,\n",
       "        8.06803200e-07, 1.84017446e-09, 8.52825622e-07, 4.01090546e-07,\n",
       "        4.50987727e-06, 9.06552415e-08, 2.16086210e-06, 1.79737995e-04,\n",
       "        4.41623733e-08, 8.76218138e-08, 3.90452143e-10, 2.66742190e-05,\n",
       "        4.57195029e-06, 2.71908948e-06, 1.51064478e-05, 1.30764423e-02,\n",
       "        9.06907826e-06, 5.40853442e-08, 1.87430737e-06, 9.80947675e-07,\n",
       "        9.18686016e-09, 5.97498968e-07, 2.85819679e-05, 4.82929863e-07,\n",
       "        2.71006314e-07, 8.05491756e-04, 1.03916627e-05, 6.08869684e-08,\n",
       "        1.86806506e-06, 7.05503744e-09, 6.91476544e-06, 2.15663545e-06,\n",
       "        6.15013732e-06, 1.25955703e-04, 2.63895781e-04, 6.77373737e-06,\n",
       "        5.14873932e-07, 1.05595882e-05, 2.64977916e-06, 1.16375282e-04,\n",
       "        4.25522956e-07, 4.12695357e-07, 5.03032958e-08, 1.09229568e-07,\n",
       "        1.04609380e-05, 1.34439371e-09, 3.94468771e-05, 2.58178011e-06,\n",
       "        1.90713604e-06, 1.21134626e-05, 3.08572368e-07, 1.19970185e-08,\n",
       "        5.51449875e-06, 7.15546867e-06, 1.40330254e-08, 3.00998136e-06,\n",
       "        4.54776732e-08, 5.73037482e-07, 1.65630624e-07, 4.03862621e-04,\n",
       "        5.29464814e-05, 3.16548744e-06, 1.35910204e-06, 6.44464944e-06,\n",
       "        1.98230197e-08, 2.46051241e-05, 6.55018448e-06, 2.31693739e-05,\n",
       "        3.32828591e-07, 4.97983274e-05, 1.99607748e-05, 3.96359248e-08,\n",
       "        2.29107354e-05, 1.78530354e-06, 3.77439591e-07, 2.67718806e-05,\n",
       "        1.23084243e-03, 1.09382171e-07, 2.84671609e-04, 6.65291200e-09,\n",
       "        1.82362578e-06, 1.10501851e-05, 1.75723122e-04, 3.81787511e-04,\n",
       "        8.16135707e-06, 4.77325868e-09, 2.08963011e-05, 1.15799150e-04,\n",
       "        3.62046831e-06, 4.19684402e-05, 5.82327386e-09, 4.76287096e-06,\n",
       "        1.21958374e-06, 3.74395745e-06, 2.05019035e-07, 2.15514307e-03,\n",
       "        1.92651933e-05, 7.57078809e-08, 9.59889690e-07, 6.16594101e-04,\n",
       "        1.10395937e-04, 3.32554600e-06, 3.97791155e-04, 4.22419488e-10,\n",
       "        2.86082695e-06, 2.87764550e-07, 9.45025095e-05, 4.45093150e-07,\n",
       "        1.23841210e-05, 5.03829051e-06, 1.61708635e-09, 1.68861894e-04,\n",
       "        1.76419769e-06, 4.01835860e-06, 3.17307531e-05, 1.31751603e-05,\n",
       "        1.49764790e-04, 1.39119493e-07, 2.59457693e-07, 8.54538121e-06,\n",
       "        3.62870051e-04, 8.42997997e-06, 2.02421827e-04, 8.41038229e-07,\n",
       "        1.89822032e-07, 4.14598915e-08, 2.03463833e-06, 2.46555999e-07,\n",
       "        6.56600747e-08, 1.07564775e-08, 3.45202054e-08, 8.66051607e-07,\n",
       "        1.01509245e-08, 1.09084408e-06, 1.92750349e-06, 3.66485921e-07,\n",
       "        2.96823739e-08, 1.08546425e-08, 1.71627362e-05, 6.58579083e-05,\n",
       "        3.43352599e-06, 4.39650375e-06, 1.13253100e-06, 4.89136801e-05,\n",
       "        1.54215504e-05, 8.17360558e-07, 2.32570710e-06, 7.54968460e-06,\n",
       "        1.47843157e-05, 6.92548610e-07, 3.13808499e-07, 3.77822033e-07,\n",
       "        1.84480905e-06, 1.16788874e-06, 4.94719870e-07, 1.77981150e-07,\n",
       "        3.62075866e-06, 1.04812689e-05, 1.29971153e-03, 8.48774744e-06,\n",
       "        1.04801264e-03, 8.25035329e-07, 1.82228030e-06, 6.06667209e-06,\n",
       "        6.27812483e-07, 1.67889667e-07, 2.17066770e-07, 1.96535225e-07,\n",
       "        2.00582508e-05, 1.27866515e-04, 1.08092991e-07, 3.52669019e-07,\n",
       "        9.00693692e-07, 6.82198079e-05, 2.97068436e-06, 6.97707856e-06,\n",
       "        4.08047799e-06, 4.27149716e-05, 2.18112968e-04, 9.70773954e-07,\n",
       "        5.39002649e-05, 2.20134643e-05, 1.76511463e-08, 4.42999317e-06,\n",
       "        1.28294153e-06, 1.17990049e-03, 2.65028830e-05, 2.64878349e-06,\n",
       "        4.05611651e-08, 1.90431911e-06, 9.48533416e-04, 6.04240313e-06,\n",
       "        4.70696887e-06, 3.73258808e-06, 6.78387721e-07, 3.88407670e-08,\n",
       "        2.47677235e-06, 2.88291119e-07, 2.01606872e-06, 2.08199867e-06,\n",
       "        4.70463465e-06, 1.44477582e-07, 9.73380811e-05, 3.85870536e-08,\n",
       "        2.30904279e-05, 2.26370685e-06, 2.50688174e-07, 4.77492504e-05,\n",
       "        4.81630536e-07, 7.61690244e-05, 1.27411374e-06, 1.27378712e-06,\n",
       "        5.54460394e-06, 3.44189402e-07, 5.68125742e-08, 8.96252277e-06,\n",
       "        9.09959563e-05, 3.33581585e-03, 1.47312392e-07, 1.80766889e-07,\n",
       "        4.45907631e-07, 2.54718515e-07, 2.53359298e-03, 4.33492886e-07,\n",
       "        1.30894523e-06, 2.14268184e-07, 1.46049308e-06, 3.41655732e-06,\n",
       "        1.76207410e-04, 9.60035209e-07, 5.95258371e-06, 1.66580776e-05,\n",
       "        2.48561012e-08, 2.60200807e-07, 3.26757181e-08, 1.28133306e-05,\n",
       "        3.48286972e-06, 1.45252500e-07, 1.12146608e-06, 1.78851747e-06,\n",
       "        1.16622650e-05, 1.28006633e-07, 1.47612003e-08, 1.98368286e-03,\n",
       "        5.71538476e-06, 2.51595583e-02, 8.13133447e-05, 2.89549490e-07,\n",
       "        4.55301176e-07, 2.49755522e-06, 6.98892848e-07, 4.45436266e-09,\n",
       "        1.04106437e-06, 1.79669416e-06, 2.86132505e-04, 7.21344713e-06,\n",
       "        1.43592763e-06, 7.28452733e-06, 4.39130326e-06, 2.38378270e-04,\n",
       "        9.33943957e-05, 2.57429846e-08, 2.94390112e-09, 9.33633279e-03,\n",
       "        1.23559090e-04, 1.27049016e-05, 3.00941042e-06, 8.46465696e-07,\n",
       "        8.33749073e-06, 1.30937349e-06, 6.14579506e-07, 4.69658151e-03,\n",
       "        4.87703037e-05, 2.60143861e-06, 2.57735810e-05, 2.17415777e-06,\n",
       "        9.92509854e-07, 6.84646075e-05, 2.22004688e-04, 7.40809185e-07,\n",
       "        1.02251803e-07, 5.80961981e-08, 1.00865955e-05, 5.80418202e-08,\n",
       "        2.72082588e-07, 3.77348010e-06, 1.27325510e-03, 3.69057670e-06,\n",
       "        1.33846572e-03, 7.81089882e-04, 2.26581710e-06, 6.59149521e-07,\n",
       "        2.62926221e-07, 1.82903739e-06, 3.98338125e-06, 8.14978762e-07,\n",
       "        3.41903905e-09, 1.49726223e-08, 1.44208530e-08, 3.74209236e-07,\n",
       "        2.73000097e-07, 1.93064276e-09, 3.88873968e-06, 2.23617860e-07,\n",
       "        2.30756223e-06, 1.60739603e-06, 4.37542098e-04, 1.59522031e-08,\n",
       "        3.99838714e-03, 3.92521855e-07, 2.33367558e-07, 2.65752599e-02,\n",
       "        4.77006906e-05, 4.78276424e-03, 7.94278640e-06, 2.74181912e-06,\n",
       "        2.83306697e-04, 9.65991831e-07, 3.66472818e-06, 4.93749430e-05,\n",
       "        1.06699124e-04, 8.94472350e-07, 1.06063214e-06, 1.37390813e-07,\n",
       "        6.85753662e-07, 1.51266605e-07, 8.13896065e-07, 3.32591526e-07,\n",
       "        9.68470033e-08, 2.28954860e-07, 4.00322449e-07, 8.67978233e-05,\n",
       "        3.06985271e-07, 2.66905205e-04, 2.49947760e-08, 1.07647247e-05,\n",
       "        7.71208361e-05, 4.49929867e-05, 2.96600479e-06, 4.47769929e-03,\n",
       "        6.06294725e-06, 3.59109312e-04, 1.08027800e-08, 4.04355296e-06,\n",
       "        7.72522799e-06, 5.02979240e-08, 1.77737420e-05, 3.50142000e-06,\n",
       "        4.44850698e-02, 2.71362602e-04, 4.48594548e-08, 7.68860176e-08,\n",
       "        1.16754507e-07, 2.77885215e-06, 1.49753657e-06, 7.28029090e-06,\n",
       "        3.99556957e-05, 2.98893958e-08, 2.38230632e-06, 1.48387940e-07,\n",
       "        3.01445129e-06, 1.85823910e-06, 7.48371690e-07, 2.18981149e-05,\n",
       "        7.93021059e-07, 2.13120938e-06, 9.86917371e-07, 1.84789383e-06,\n",
       "        9.61074038e-05, 6.69046472e-07, 3.65177425e-06, 1.08248096e-04,\n",
       "        1.84509304e-07, 4.31774697e-06, 1.84551982e-06, 1.34543079e-08,\n",
       "        5.56181323e-09, 1.12337570e-08, 4.65420962e-05, 3.94995914e-06,\n",
       "        2.59135617e-04, 1.36122674e-06, 7.49490209e-05, 3.92200036e-07,\n",
       "        1.51588858e-04, 4.91399952e-08, 3.77914125e-06, 1.27022656e-06,\n",
       "        2.15911538e-08, 2.47777585e-08, 2.37427614e-07, 6.35333549e-07,\n",
       "        1.22939946e-05, 2.49591097e-03, 4.19458302e-05, 6.21597648e-01,\n",
       "        1.65602146e-03, 1.33420099e-02, 4.40637223e-06, 8.48955665e-07,\n",
       "        2.17196725e-06, 1.77003210e-06, 8.50207793e-10, 1.64550009e-08,\n",
       "        7.56202826e-06, 3.87513455e-06, 8.03259297e-07, 5.21473964e-07,\n",
       "        9.33859792e-06, 1.39517013e-07, 4.84374732e-06, 7.70367478e-07,\n",
       "        4.04339460e-08, 1.34622709e-07, 9.06913868e-08, 1.34657325e-08,\n",
       "        3.59125885e-08, 9.12423914e-09, 3.40118504e-07, 3.41634859e-06,\n",
       "        1.57520174e-06, 1.04868730e-06, 7.01866476e-09, 1.20070737e-07,\n",
       "        4.81964335e-08, 3.34515420e-07, 9.57807970e-06, 2.57387107e-08,\n",
       "        3.83323112e-07, 6.25803750e-06, 1.45590484e-05, 1.42334748e-08,\n",
       "        2.38515945e-08, 3.89092838e-06, 3.73646287e-08, 1.03849914e-06,\n",
       "        2.94444908e-05, 9.03339714e-09, 5.55321094e-07, 4.37111476e-06,\n",
       "        6.27732277e-07, 7.44751333e-07, 1.25527959e-06, 3.31318756e-06,\n",
       "        5.12518527e-05, 2.11080373e-06, 8.36187337e-06, 1.40443603e-07,\n",
       "        1.80107200e-08, 3.23831166e-08, 6.14367357e-09, 7.39533268e-10,\n",
       "        2.97146463e-09, 2.65671844e-07, 1.38433919e-07, 3.75867476e-10,\n",
       "        3.44940122e-06, 1.51799995e-05, 5.55045254e-09, 8.03328305e-07,\n",
       "        1.91839078e-08, 1.92435252e-07, 2.27402680e-07, 8.30909357e-07,\n",
       "        1.46965027e-08, 1.39900260e-08, 5.87906002e-10, 4.73432706e-08,\n",
       "        8.95379131e-08, 5.37661862e-08, 4.90121099e-09, 5.14067381e-07,\n",
       "        6.63952875e-08, 7.43792639e-10, 5.43953774e-07, 7.23019923e-07]],\n",
       "      dtype=float32)"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "predictions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "id": "25764787",
   "metadata": {},
   "outputs": [],
   "source": [
    "k=predictions.argmax()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "id": "58a727aa",
   "metadata": {},
   "outputs": [],
   "source": [
    "results = imagenet_utils.decode_predictions(predictions)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "id": "43be69b7",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "[[('n06794110', 'street_sign', 0.62159765),\n",
       "  ('n02708093', 'analog_clock', 0.17901841),\n",
       "  ('n04485082', 'tripod', 0.04448507),\n",
       "  ('n04355338', 'sundial', 0.02657526),\n",
       "  ('n04118776', 'rule', 0.025159558)]]"
      ]
     },
     "execution_count": 20,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "results"
   ]
  },
  {
   "cell_type": "raw",
   "id": "d7a49d87",
   "metadata": {},
   "source": [
    "9705836970 ibm\n",
    "\n",
    "9492443678 nuzvid\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "088d7cd0",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "91a55040",
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.8"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
