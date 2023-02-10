# Linear filters - functions

  - [Gaussian smoothing filter](#gaussian-smoothing-filter)
  - [Mean filter](#mean-filter)
  - [Linear filter](#linear-filter)
  - [Sharpness estimation](#sharpness-estimation)

## Gaussian smoothing filter
In 2-D, an isotropic (i.e. circularly symmetric) Gaussian has the form:


![](https://latex.codecogs.com/svg.latex?\Large&space;G(x,y)=\frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}) 

where ![](https://latex.codecogs.com/svg.latex?&space;\sigma)  is the standard deviation of the distribution. This distribution is shown in the following Figure


<p align="center">
  <img width="60%" height="60%" src=README_images/gauss2.gif>
  <center><em>2-D Gaussian distribution with mean (0,0) and sigma=1</em></center>
</p>



The effect of Gaussian smoothing is to blur an image, in a similar fashion to the mean filter. The degree of smoothing is determined by the standard deviation of the Gaussian. The Gaussian outputs a "weighted average" of each pixel's neighborhood, with the average weighted more towards the value of the central pixels. This is in contrast to the mean filter's uniformly weighted average. Because of this, a Gaussian provides gentler smoothing and preserves edges better than a similarly sized mean filter.


By looking at the following figure it shows the frequency responses of a 1-D mean filter with width 5 and also of a Gaussian filter with ![](https://latex.codecogs.com/svg.latex?&space;\sigma=3).

<p align="center">
  <img width="70%" height="70%" src=README_images/gausfreq.gif>
</p>

Both filters attenuate high frequencies more than low frequencies, but the mean filter exhibits oscillations in its frequency response. So by choosing an appropriately sized Gaussian filter we can be fairly  confident about what range of spatial frequencies are still present in the image after filtering, which is not the case of the mean filter. 


```Matlab
function output_img = gaussian_smoothing_filter (img, a, b, sigma)
[m ,n] = size(img);
output_img = img;
mask = zeros(a,b);

for x=1:a
    for y=1:b
        mask(x,y)=(1/(2*pi*sigma^2))*(exp(-((x-2)^2+(y-2)^2)/(2*sigma^2)));
    end
end
sum=0;
start = ceil(a/2);

for x = start:1:m-start
    for y = start:1:n-start
        for i = 1:1:a
            for j = 1:1:b
                sum = sum + (img(x-start+i,y-start+j)*mask(i,j));
            end
        end
        output_img(x,y) = sum;
        sum = 0;
    end
end
output_img = uint8(output_img);
end
```

## Mean filter
The idea of mean filtering is simply to replace each pixel value in an image with the mean (`average') value of its neighbors, including itself. This has the effect of eliminating pixel values which are unrepresentative of their surroundings. Mean filtering is usually thought of as a convolution filter. Like other convolutions it is based around a kernel, which represents the shape and size of the neighborhood to be sampled when calculating the mean. Often a 3×3 square kernel is used, as shown in Figure 1, although larger kernels (e.g. 5×5 squares) can be used for more severe smoothing. 

<p align="center">
  <img width="30%" src=README_images/mean3x3.gif>
  <center><em>3×3 averaging kernel often used in mean filtering</em></center>
</p>



```Matlab
function output_img = mean_filter(img,a,b)
start = ceil(a/2);
[m ,n] = size(img);
output_img = img;

for x = start:1:m-start
    for y = start:1:n-start
        for i = 1:1:a
            for j = 1:1:b
                temp_img(i,j) = img(x-start+i,y-start+j);
            end
        end
        output_img(x,y) = mean(temp_img(:));
    end
end

output_img = uint8(output_img);

end
```

## Linear filter

In image processing, a kernel, convolution matrix, or mask is a small matrix used for blurring, sharpening, embossing, edge detection, and more. This is accomplished by doing a convolution between the kernel and an image.

![](https://latex.codecogs.com/svg.latex?&space;g(x,y)=\omega*f(x,y)=\sum_{dx=-a}^{a}{\sum_{dy=-b}^{b}{\omega(dx,dy)f(x+dx,y+dy)}})


where ![](https://latex.codecogs.com/svg.latex?&space;g(x,y)) is the filtered image, ![](https://latex.codecogs.com/svg.latex?&space;f(x,y)) is the original image, ![](https://latex.codecogs.com/svg.latex?&space;\omega) is the filter kernel. 

Depending on the element values, a kernel can cause a wide range of effects.
```Matlab
function output_img = linear_filter(img, mask)

[m ,n] = size(img);
sum=double(0);
a = size(mask,1);
b = size(mask,2);
mask = double(mask);
img = double(img);
output_img = zeros(m,n);

start = ceil(a/2);
for x = start:1:m-start
    for y = start:1:n-start
        for i = 1:1:a
            for j = 1:1:b
                sum = sum + double((img(x-start+i,y-start+j)*mask(i,j)));
            end
        end
        output_img(x,y) = double(sum);
        sum = 0;
    end
end
output_img = uint8(output_img);

end
```

## Sharpness estimation

Sharpness estimation using gradients

```Matlab
function sharpness = estimate_sharpness(img)
G=double(img);
[Gx, Gy]=gradient(G);

S=sqrt(Gx.*Gx+Gy.*Gy);
sharpness=sum(sum(S))./(numel(Gx));

end
```
