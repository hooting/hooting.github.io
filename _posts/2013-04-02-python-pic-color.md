---
layout: post
title: "python判断一张图片的主色调"
subtitle: ""
category: Python
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Python]
---

python判断一张图片的主色调，多个颜色

    #!/usr/bin/env python
    #encoding:utf8

    import colorsys
    from PIL import Image
    import optparse


    def get_dominant_color(image):
        """
        Find a PIL image's dominant color, returning an (r, g, b) tuple.
        """

        image = image.convert('RGBA')

        # Shrink the image, so we don't spend too long analysing color
        # frequencies. We're not interpolating so should be quick.
        # image.thumbnail((200, 200))

        max_score = 1
        dominant_color = []

        for count, (r, g, b, a) in image.getcolors(image.size[0] * image.size[1]):
            # Skip 100% transparent pixels
            if a == 0:
                continue

            # Get color saturation, 0-1
            saturation = colorsys.rgb_to_hsv(r / 255.0, g / 255.0, b / 255.0)[1]

            # Calculate luminance - integer YUV conversion from
            # http://en.wikipedia.org/wiki/YUV
            y = min(abs(r * 2104 + g * 4130 + b * 802 + 4096 + 131072) >> 13, 235)

            # Rescale luminance from 16-235 to 0-1
            y = (y - 16.0) / (235 - 16)

            # Ignore the brightest colors
            if y > 0.9:
                continue

            # Calculate the score, preferring highly saturated colors.
            # Add 0.1 to the saturation so we don't completely ignore grayscale
            # colors by multiplying the count by zero, but still give them a low
            # weight.
            score = (saturation + 0.1) * count
            if score > max_score:
                max_score = score
                dominant_color.append((r, g, b))

        return dominant_color

    def main():
        img = Image.open("meitu.jpg")
        colors = get_dominant_color(img)
        for item in colors:
            print '#%02x%02x%02x' % item

    if __name__ == '__main__':
        main()
