# Linear filters - functions

  - [Gaussian smoothing filter](#gaussian-smoothing-filter)
  - [Mean filter](#mean-filter)
  - [Linear filter filter](#linear-filter-filter)
  - [Sharpness estimation](#sharpness-estimation)

## Gaussian smoothing filter

$G(x,y) =  \frac{1}{2 \pi \sigma^2} e^{-\frac{x^2 + y^2}{2 \sigma^2}}$

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