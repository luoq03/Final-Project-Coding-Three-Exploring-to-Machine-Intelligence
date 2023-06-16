# Generating Cursive Font / Oriental Aesthetic Landscape Painting/ English Handwritten Font / Oil Painting Color Landscape Painting images through DCGAN
![download (38)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/47dde31a-b44c-4e57-a223-37d2afeb0499)
![download (39)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/60b7e9aa-1e21-4bce-b076-d4e059c6a07f)
![download (40)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/4ce12b6a-ef14-40dc-bfcc-a68cd09c044c)
![download (41)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/6c681593-0390-403a-8748-ed4e12eda797)


## Inspiration ##

In my view, generating Eastern and Western handwriting fonts and painting images through Deep Convolutional Generative Adversarial Networks is an innovative and inspiring idea. The use of this technology not only explores the infinite possibilities in the field of art, but also delves into two distinctly different cultural expressions.

Take fonts as an example, each font contains cultural characteristics and historical accumulation. Western fonts attract people with their unique line aesthetics and typography design, while Eastern fonts show the strength and fluidity of strokes. If we could use DCGAN to learn and generate these two styles of fonts, the results might bring a completely new artistic experience. It could be a novel font style or a modern interpretation of traditional culture.

In terms of painting, Eastern and Western art have fundamental differences. Western painting emphasizes light and shadow effects and perspective, while Eastern painting emphasizes a ethereal aesthetic and the flow of lines. The paintings generated by DCGAN can not only absorb the essence of these two artistic styles, but may also create new visual effects in the collision, inspiring new artistic inspirations.

In addition, this technology may also inspire our thinking about artificial intelligence and innovation. For instance, can AI truly understand the essence of art? Can it cross cultural boundaries and create truly unique pieces of art? The exploration of these questions will undoubtedly push our understanding of AI to a new level.

In conclusion, generating Eastern and Western handwriting fonts and painting images through DCGAN is not only a project with practical application value, but also a challenge and expansion of our own cognition, it's an interesting experiment where art and technology intersect.

## Project File Download URL (Google Drive) : ##

### Work 1/4 Generate Cursive Font  ###

https://colab.research.google.com/drive/1FHlYZHw31qLUP2-qhhd5cd-q2w069f6v#scrollTo=ion3gKGJ084G

### Work 2/4 Generate Oriental Aesthetic Landscape Painting  ###

https://colab.research.google.com/drive/16Oq25cVU4FI5M6-3qJKyOLPQ-QqgMMat#scrollTo=7orlWJZNx978

### Work 3/4 Generate English Handwritten Font  ###

https://colab.research.google.com/drive/1CkcOXvcGQ25E6_Iwd8EJoIR3HjdwgI2W#scrollTo=5pDuG1Kx0fW0

### Work 4/4 Generate Oil Painting Color Landscape Painting  ###

https://colab.research.google.com/drive/1SELv1vqRPXpVQMQuMnt_etvRkw79PQGn#scrollTo=OReHnZSUzeOI

## Dataset Information ##

### URL for reference dataset ###

https://www.kaggle.com/datasets/myzhang1029/chinese-landscape-painting-dataset

https://www.kaggle.com/datasets/bai224/chinese-calligraphy-characters-image-set

https://www.kaggle.com/datasets/tejasreddy/iam-handwriting-top50

https://www.kaggle.com/datasets/robgonsalves/impressionistlandscapespaintings

### Screenshot of the dataset modified by myself ###

![WeChat Photo Editor_20230616002030 (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/2cdb505e-6845-477c-8442-59a18821d2f4)
![WeChat Photo Editor_20230616001841 (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/d1e7353c-a7af-4bbc-922c-b57dbc880b26)


##  Main Code Structure and Explanation ##
Since the foundational code for the four works is the same, I will explain them together here.
** ### In the following code, I made some attempts to improve the code:  ###**

**1. I used more convolutional layers and deeper networks, or repeated training with the same network depth to enhance the quality of the generated outputs.**

**2. I tried using different optimizers and adjusting the values of the loss function.**

**3. I optimized the dataset to have better performance during model training, such as using higher resolution images.**

**4. I experimented with using more epochs for training.**

**5. I tried adjusting the parameter for label smoothing.**

###  Import necessary libraries and modules ###

``` import os
import numpy as np
import matplotlib.pyplot as plt
import warnings
from tqdm.notebook import tqdm
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras.preprocessing.image import load_img, array_to_img
from tensorflow.keras.models import Sequential, Model
from tensorflow.keras import layers
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.losses import BinaryCrossentropy
```

### Load the Dataset ###

Create the base directory
```
BASE_DIR = '/kaggle/input/newnewnewnew/dcgan/'
```

Create a list which will contain all the dataset images

```
image_paths = []
for image_name in os.listdir(BASE_DIR):
    image_path = os.path.join(BASE_DIR, image_name)
    image_paths.append(image_path)
```

### Visualize the Image Dataset ###

```
# to display grid of images (5x5)
plt.figure(figsize=(30, 30))
temp_images = image_paths[:25]
index = 1

for image_path in temp_images:
    plt.subplot(5, 5, index)
    # load the image
    img = load_img(image_path)
    # convert to numpy array
    img = np.array(img)
    # show the image
    plt.imshow(img)
    plt.axis('off')
    # increment the index for next image
    index += 1
```

### Preprocess the Image Dataset ###
```
train_images = [np.array(load_img(path)) for path in tqdm(image_paths)]
train_images = np.array(train_images)
```
See the shape of the image
```
train_images[0].shape
```
Reshape the numpy array
```
train_images = train_images.reshape(train_images.shape[0], 64, 64, 3).astype('float32')
```
Normalize the image
```
train_images = (train_images - 127.5) / 127.5
```

### Create Generator & Discriminator Models ###
Initialize the required values to the variables: 

The WEIGHT_INIT variable represents the weight initializer used to initialize the weights of the neural network model. In this case, it is set to a random normal initializer with a mean of 0.0 and a standard deviation of 0.02.

The CHANNELS variable represents the number of channels in the image data. In this case, it is set to 3, indicating that the images are in RGB color format.
```
LATENT_DIM = 100
WEIGHT_INIT = keras.initializers.RandomNormal(mean=0.0, stddev=0.02)
CHANNELS = 3 
```
Generator Model : create new images similar to training data from random noise
```
model = Sequential(name='generator')
model.add(layers.Dense(8 * 8 * 512, input_dim=LATENT_DIM))
model.add(layers.ReLU())
model.add(layers.Reshape((8, 8, 512)))
model.add(layers.Conv2DTranspose(256, (4, 4), strides=(2, 2), padding='same', kernel_initializer=WEIGHT_INIT))
model.add(layers.ReLU())
model.add(layers.Conv2DTranspose(128, (4, 4), strides=(2, 2), padding='same', kernel_initializer=WEIGHT_INIT))
model.add(layers.ReLU())
model.add(layers.Conv2DTranspose(64, (4, 4), strides=(2, 2), padding='same', kernel_initializer=WEIGHT_INIT))
model.add(layers.ReLU())
model.add(layers.Conv2D(CHANNELS, (4, 4), padding='same', kernel_initializer=WEIGHT_INIT, activation='tanh'))
```
![3afb4c0a29e05c80f6ad102767e13c0](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/80925c69-f6aa-4658-a7d6-9681af243d71)

Discriminator model : classify the image from the generator to check whether it real (or) fake images.

(64, 64, 3) is the dimensions of the image , the first two dimensions represent the spatial dimensions (height and width), and the last dimension represents the number of channels
```
model = Sequential(name='discriminator')
input_shape = (64, 64, 3)
alpha = 0.2

# create conv layers
model.add(layers.Conv2D(64, (4, 4), strides=(2, 2), padding='same', input_shape=input_shape))
model.add(layers.BatchNormalization())
model.add(layers.LeakyReLU(alpha=alpha))

model.add(layers.Conv2D(128, (4, 4), strides=(2, 2), padding='same', input_shape=input_shape))
model.add(layers.BatchNormalization())
model.add(layers.LeakyReLU(alpha=alpha))

model.add(layers.Conv2D(256, (4, 4), strides=(2, 2), padding='same', input_shape=input_shape))
model.add(layers.BatchNormalization())
model.add(layers.LeakyReLU(alpha=alpha))

model.add(layers.Conv2D(256, (4, 4), strides=(2, 2), padding='same', input_shape=input_shape))
model.add(layers.BatchNormalization())
model.add(layers.LeakyReLU(alpha=alpha))

model.add(layers.Flatten())
model.add(layers.Dropout(0.3))

# output class
model.add(layers.Dense(1, activation='sigmoid'))

discriminator = model
discriminator.summary()
```
![fab7b6d92b75af8c8f8a138219da6f9](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/917115dc-cb51-471e-bc9d-2379e4a760c0)

### Create DCGAN ###
Train both the generator and discriminator models at same time and update the weights with customized loss functions
```
class DCGAN(keras.Model):
    def __init__(self, generator, discriminator, latent_dim):
        super().__init__()
        self.generator = generator
        self.discriminator = discriminator
        self.latent_dim = latent_dim
        self.g_loss_metric = keras.metrics.Mean(name='g_loss')
        self.d_loss_metric = keras.metrics.Mean(name='d_loss')
        
    @property
    def metrics(self):
        return [self.g_loss_metric, self.d_loss_metric]
    
    def compile(self, g_optimizer, d_optimizer, loss_fn):
        super(DCGAN, self).compile()
        self.g_optimizer = g_optimizer
        self.d_optimizer = d_optimizer
        self.loss_fn = loss_fn
        
    def train_step(self, real_images):
        # get batch size from the data
        batch_size = tf.shape(real_images)[0]
        # generate random noise
        random_noise = tf.random.normal(shape=(batch_size, self.latent_dim))
        
        # train the discriminator with real (1) and fake (0) images
        with tf.GradientTape() as tape:
            # compute loss on real images
            pred_real = self.discriminator(real_images, training=True)
            # generate real image labels
            real_labels = tf.ones((batch_size, 1))
            # label smoothing
            real_labels += 0.05 * tf.random.uniform(tf.shape(real_labels))
            d_loss_real = self.loss_fn(real_labels, pred_real)
            
            # compute loss on fake images
            fake_images = self.generator(random_noise)
            pred_fake = self.discriminator(fake_images, training=True)
            # generate fake labels
            fake_labels = tf.zeros((batch_size, 1))
            d_loss_fake = self.loss_fn(fake_labels, pred_fake)
            
            # total discriminator loss
            d_loss = (d_loss_real + d_loss_fake) / 2
            
        # compute discriminator gradients
        gradients = tape.gradient(d_loss, self.discriminator.trainable_variables)
        # update the gradients
        self.d_optimizer.apply_gradients(zip(gradients, self.discriminator.trainable_variables))
        
        
        # train the generator model
        labels = tf.ones((batch_size, 1))
        # generator want discriminator to think that fake images are real
        with tf.GradientTape() as tape:
            # generate fake images from generator
            fake_images = self.generator(random_noise, training=True)
            # classify images as real or fake
            pred_fake = self.discriminator(fake_images, training=True)
            # compute loss
            g_loss = self.loss_fn(labels, pred_fake)
            
        # compute gradients
        gradients = tape.gradient(g_loss, self.generator.trainable_variables)
        # update the gradients
        self.g_optimizer.apply_gradients(zip(gradients, self.generator.trainable_variables))
        
        # update states for both models
        self.d_loss_metric.update_state(d_loss)
        self.g_loss_metric.update_state(g_loss)
        
        return {'d_loss': self.d_loss_metric.result(), 'g_loss': self.g_loss_metric.result()}
```
 Plot some images for each epoch （The generated images are then denormalized by multiplying by 127.5 and adding 127.5 to bring the pixel values back to the range of [0, 255]）
```
class DCGANMonitor(keras.callbacks.Callback):
    def __init__(self, num_imgs=25, latent_dim=100):
        self.num_imgs = num_imgs
        self.latent_dim = latent_dim
        # create random noise for generating images
        self.noise = tf.random.normal([25, latent_dim])

    def on_epoch_end(self, epoch, logs=None):
        # generate the image from noise
        g_img = self.model.generator(self.noise)
        # denormalize the image
        g_img = (g_img * 127.5) + 127.5
        g_img.numpy()
        
        fig = plt.figure(figsize=(8, 8))
        for i in range(self.num_imgs):
            plt.subplot(5, 5, i+1)
            img = array_to_img(g_img[i])
            plt.imshow(img)
            plt.axis('off')
        # plt.savefig('epoch_{:03d}.png'.format(epoch))
        plt.show()
        
    def on_train_end(self, logs=None):
        self.model.generator.save('generator.h5')
```
Initialize the DCGAN model 
```
dcgan = DCGAN(generator=generator, discriminator=discriminator, latent_dim=LATENT_DIM)
```
Compile the DCGAN model,（I attempted to use different optimizers and adjust the value of beta_1.）BinaryCrossentropy is used for loss function

![20a7d7dcdf6dbd2a44263833ed29ba3](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/6b5d37e7-66e2-465c-b83e-1544088c24c4)

```
D_LR = 0.0001 
G_LR = 0.0003
dcgan.compile(g_optimizer=RMSprop(learning_rate=G_LR), d_optimizer=RMSprop(learning_rate=D_LR),
loss_fn=MeanSquaredError())
```
### Training the DCGAN model ###
I attempted to adjust the value of different EPOCHs.
```
N_EPOCHS = 70
dcgan.fit(train_images, epochs=N_EPOCHS, callbacks=[DCGANMonitor()])
```
### Generating new images using trained dcgan model ###
```
noise = tf.random.normal([1, 100])
fig = plt.figure(figsize=(3, 3))
# generate the image from noise
g_img = dcgan.generator(noise)
# denormalize the image
g_img = (g_img * 127.5) + 127.5
g_img.numpy()
img = array_to_img(g_img[0])
plt.imshow(img)
plt.axis('off')
# plt.savefig('epoch_{:03d}.png'.format(epoch))
plt.show()
```
## Work 1/4 Generate Cursive Font  ##

Epoch 10/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/d8a38714-091e-4efe-ab9d-6ca5d8e121af)

Epoch 20/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/36ecf015-1d72-45d1-b635-5792c98c5a8d)

Epoch 30/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/cf8163ef-861c-478c-bdf8-d061b5984e59)

Epoch 40/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/b24cdb2c-3974-4b72-bee7-07b72fabc0f9)

Epoch 50/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/9ff3cf36-69c9-47e4-a6f4-6fbfac475e98)

Epoch 60/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/54994eca-450f-439a-b814-e9ceaf338eb8)

Epoch 70/70

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/533d027f-4faa-4c4e-9734-5acc4639e2bf)

### **Comparison between Original Dataset and Generated Data** ###

![download (24)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/afc69e8d-2fa4-4ac2-960c-748d376f7276)
![download (25)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/12ace5e8-ea15-4301-8c86-d1454234f3f2)

## Work 2/4 Generate Oriental Aesthetic Landscape Painting  ##

Epoch 10/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/420d93a1-0f99-4510-8e02-a1e3b99510ac)


Epoch 20/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/b1e2d0da-1280-4b67-9239-7e858812725b)

Epoch 30/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/d3b4a7bd-3e67-49cd-a764-967551d7abb9)

Epoch 40/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/4a6f00d9-1f90-4a57-9601-96b0e140240b)

Epoch 50/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/e723ad66-b7fe-4885-8c60-edc625781fb8)

Epoch 60/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/f68ce1d5-da9e-410d-96c7-3faf2e618cc1)

Epoch 70/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/f21fb567-41ad-4a86-baaa-49b6ae44d477)

Epoch 80/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/bbd24c9e-49a6-4ce1-84d8-130e56825963)

Epoch 90/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/cfaf9b90-8f9f-427b-a6bc-5aae1228cfa5)

Epoch 100/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/a23bb52b-0201-4a86-b8f0-21bdeff6db91)

Epoch 110/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/95b7899a-ff60-4395-bcfa-2f5124b530a1)

Epoch 120/120

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/81a97596-5a95-4e9e-8a45-c3bc3871529d)

### **Comparison between Original Dataset and Generated Data** ###

![download (14)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/202ac172-e3c8-4294-ba2b-86c6622c8f30)
![download (15)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/63919239-4662-406d-9693-c0a85cee59fe)

## Work 3/4 Generate English Handwritten Font  ##

Epoch 10/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/f2b19fb0-5974-4001-a6d5-a104d7d15512)

Epoch 20/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/133ae899-04f0-4797-9ac0-f9b91564c4ba)

Epoch 30/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/56f9ebff-d44b-4961-ad7b-1706c4b64419)

Epoch 40/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/8d5683e7-34a8-463c-93bc-a20e6444200c)

Epoch 50/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/b0c7eb4b-b404-4f5d-a173-d4cd2a3df6ef)

Epoch 60/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/e88c3a7c-ddc3-4f79-a3e5-51a177005068)

Epoch 70/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/c34fff4f-454a-43ce-9995-3edba26518d4)

Epoch 80/80

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/3d008814-cd39-4fcd-89d8-93c0009e89f4)

### **Comparison between Original Dataset and Generated Data** ###

![download (16)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/2a1344c5-6ac3-45bf-b7fb-8b298ad2cc7e)
![download (17)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/33361e5c-3ceb-47d6-97c3-81e85a27a6e2)

## Work 4/4 Generate Oil Painting Color Landscape Painting  ##

Epoch 10/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/b0909048-b66e-4bfe-8905-3d49f08941a6)

Epoch 20/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/2320eab7-037e-4d8f-b249-7d13c4062fd0)

Epoch 30/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/4634f74f-0f65-4af0-bd36-fb0dc24f858b)

Epoch 40/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/47dfcd09-c105-4c18-8be4-8157b935c250)

Epoch 50/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/6b433954-4a88-4584-aea0-5f9e29529408)

Epoch 60/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/8af1671f-36a1-466c-b11b-d10a763259ef)

Epoch 70/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/b72dd803-9caf-4678-8361-0a719b3145fa)

Epoch 80/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/1df3a3d9-a464-4c14-8158-c74122152e92)

Epoch 90/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/8bcabb7c-9780-4469-87a7-58b3b5aa578c)

Epoch 100/100

![image](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/c6207a1f-dbd3-4f20-b6e0-9df18fd3be32)

### **Comparison between Original Dataset and Generated Data** ###

![download (18)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/3f8e2057-ea8a-4524-9906-6e017e1e5078)
![download (19)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/fce579bc-ff56-49e2-9af4-0428ade622e5)

## New images using trained dcgan model ##
![246210840-bb484e6e-1103-41c2-b254-fe063fefe243 (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/c68f3812-ca8c-4b33-957e-28b5b32cb608)
![246210886-fc97ff28-d646-475a-8388-206e898f08ea (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/66f756d1-433e-487b-b9be-00a8b736b02e)

![246210933-27ee36fd-d0e0-408d-8a9f-258b72692683 (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/fc0f206c-25d0-4592-af0c-4c99be29ec21)
![246211005-f7870e9a-92ca-4a31-b482-a071f0989116 (1)](https://github.com/luoq03/Final-Project-Coding-Three-Exploring-to-Machine-Intelligence/assets/57748663/8ee4162f-aff9-49ed-82cf-fa082ac53f27)

## Summary ##
The images of cursive script, English handwriting, Eastern landscape paintings, and oil landscape paintings generated by DCGAN each exhibit unique artistic styles and expressions.

Firstly, the images of cursive script generated by DCGAN present a rough yet powerful writing style. These cursive images are filled with dynamics and vitality, with lines crisscrossing, forming a unique artistic impression. I sent the generated images to my friend who studies calligraphy, who thought the generated images seemed like masterpieces.

In contrast are the English handwritten images generated by DCGAN. These images show that the writing style of English letters presents personalized expressions. Perhaps due to the limited number of English letters, they do not seem to be the combinations of English letters we are familiar with.

In terms of Eastern landscape paintings, the images generated by DCGAN exhibit the traditional Eastern artistic style. These images present elements such as mountains, streams, pavilions, towers, etc., conveying a sense of tranquility and mystery. However, the generated style is somewhat monotonic compared to the dataset provided.

On the other hand, the images of oil landscape paintings generated by DCGAN present a rich oil texture and detailed depiction. Oil landscape painting images usually have strong color contrasts and light and dark effects, creating a strong visual impact and expressiveness.

In summary, the images of cursive script, English handwriting, Eastern landscape paintings, and oil landscape paintings generated by DCGAN each have their own characteristics. The cursive images show a rough yet powerful writing style, the English handwriting images present personalized English letter writing, the Eastern landscape painting images convey a sense of tranquility and mystery, and the oil landscape painting images present a rich color and detailed depiction. These four kinds of images each demonstrate different artistic styles and ways of expression, showcasing the potential of DCGAN in imitating and creating artistic works.

## Project References ##
https://github.com/aswintechguy/Deep-Learning-Projects

https://www.tensorflow.org/tutorials/generative/dcgan?hl=zh-cn

https://www.youtube.com/watch?v=HxD-M-jTmEA&t=979s

https://gsurma.medium.com/image-generator-drawing-cartoons-with-generative-adversarial-networks-45e814ca9b6b

https://www.kaggle.com/code/greg115/image-generator-dcgan-the-simpsons-dataset

https://arxiv.org/abs/1701.00160
