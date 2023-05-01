Download Link: https://assignmentchef.com/product/solved-elec4622-project-1
<br>
<h1></h1>

This is the first of three mini-projects to be demonstrated and assessed within the regular scheduled laboratory sessions. This project is due in the Week 9 laboratory session. The project is worth a nominal 10 marks, out of the 30 marks available for the laboratory component of Elec4622/Elec9722. However, there are optional elements which can attract a lot of potential bonus marks.

Get ready to put a lot of work into this project, but also to learn a huge amount from it! Success will build confidence, so invest the effort and use your remaining laboratory sessions wisely.

In this project, you will implement and explore the implications of various strategies for image resampling. To simplify matters, we consider only two cases here:

<ol>

 <li>Reducing the size of an image by a factor of 5<em>/</em>3 in each direction — i.e., zoom-out.</li>

 <li>Increasing the size of an image by a factor of 5<em>/</em>3 in each direction — i.e., zoom-in.</li>

</ol>

<h1>2           A Brief Review of the Theory</h1>

Let <em>x</em>[<strong>n</strong>] denote a discrete digital image, which we assume to arise from the sampling of an underlying bandlimited signal <em>f </em>(<strong>s</strong>). This continuous signal can then be recovered via

<em>f </em>(<strong>s</strong>) = [<em>x</em>[<strong>n</strong>]<em>q </em>(<strong>s</strong>−<strong>n</strong>)

<strong>n</strong>

where the interpolation kernel ideally satisfies <em>q </em>(<strong>s</strong>) = sinc(<em>s</em><sub>1</sub>)sinc(<em>s</em><sub>2</sub>).

<h2>2.1           Expansion</h2>

To expand the image by a factor of 5<em>/</em>3, we simply need to resample <em>f </em>(<strong>s</strong>) at the locations <strong>s </strong>= <u><sup>3</sup></u><sub>5</sub><strong><u><sup>m</sup></u></strong>, obtaining

(1)

<strong>n</strong>

Resizing is a separable operation. This can be deduced by recognizing that the <em>q </em>(<strong>s</strong>) interpolation kernel used in the above formula is separable. However, it is also intuitively obvious that we can first expand each image row and then expand each column of the result, or vice-versa. In view of this, we can simplify our analysis (and implementation) by considering the expansion in one dimension only. In this case, equation

(1) becomes

In the expanded result, those locations whose indices are multiples of 5 are found from

<em>,</em>

since <em>q </em>(<em>s</em>) = sinc(<em>s</em>) is equal to <em>δ </em>(<em>n</em>) at integer locations <em>n</em>. This just means that every second input sample aligns perfectly with every fifth output sample — no interpolation is required. The other locations are found from

where

That is, <em>q</em><em>σ </em>[<em>n</em>] is the digital filter whose PSF is obtained by delaying (translating to the right) the basic sinc(<em>s</em>) function by <em>σ </em>and then sampling it at the integer locations. To obtain, we convolve <em>x </em>by and keep only the locations 3<em>m</em>+1 from the result. Similarly, we obtain by convolving <em>x </em>with and keeping only the locations 3<em>m </em>+ 1, and so forth.

It is better computationally, and much simpler conceptually, to rewrite the above expressions as inner products. In this case we have

— noting that <em>q </em>(<em>s</em>) = <em>q </em>(−<em>s</em>)

and, similarly,

What this means is that you obtain for any given <em>m </em>by translating the function <em>q</em>−0<em><sub>.</sub></em><sub>4 </sub>[<em>n</em>] across by 3<em>m </em>+ 1 samples and taking the dot product with the input sequence — note that 3<em>m </em>+ 1 is the nearest integer location to the desired location of . Similarly, you obtain by translating the function <em>q</em><sub>0<em>.</em>2 </sub>[<em>n</em>] by 3<em>m </em>+ 1 and taking its dot product with the input sequence — again, 3<em>m </em>+ 1 is just the nearest integer to .

In the end, you need to evaluate and store 4 different inner product “kernels,” corresponding to <em>q</em>±0<em><sub>.</sub></em><sub>2 </sub>[<em>n</em>] and <em>q </em>0<em><sub>.</sub></em><sub>4 </sub>[<em>n</em>]. Then, in every group of 5 output samples, one of them is a direct copy of the corresponding ± input sample, and each of the other four are obtained by taking inner products with the relevant <em>q </em>kernel, translated to the sample location which is closest to the one you want.

Of course, <em>q</em>±<sub>0<em>.</em>2 </sub>[<em>n</em>] and <em>q</em>±0<em><sub>.</sub></em><sub>4 </sub>[<em>n</em>] are infinite extent sequences, so you will have to window them or apply some other approximation technique in practice. You are now in a position to develop an implementation that is closely related to what you have seen in Lab 2.

Before proceeding, it is very strongly recommended (pretty much mandatory) that you draw up a figure to convince yourself that you thoroughly understand exactly how each group of 5 output samples on a line is derived from input samples via inner products. Your figure should depict exactly what inner products are involved. The figure is useless unless you can actually put windowed sinc sample values into your figure and work out exactly what you would get for the interpolated value at a given location, based on a given set of inputs. If your program has bugs, you will need this figure, since it tells you exactly what to expect at a given location, based on the inputs that may be available. Debugging is all about comparing your expectations against what is actually happening at a given location, as reported live via the debugger. Actually using a debugger is the most efficient way to discover and fix problems, so if all you have ever done is use print statements, now is the time to make sure you know how to use the integrated debugger in the Visual Studio platform (or another platform if you are using something else).

<h2>2.2           Reduction</h2>

To reduce the image by a factor of , we need to resample <em>f </em>(<strong>s</strong>) at multiples of . However, to avoid aliasing, we must be sure first to reduce the bandwidth of <em>f </em>(<strong>s</strong>) so that <em>f</em>ˆ(ω) ≈ 0 for ω∈<em>/ </em>[−3<em>π/</em>5<em>,</em>3<em>π/</em>5]<sup>2</sup>. Equivalently, we first shrink <em>f </em>(<strong>s</strong>) by a factor of , bandlimit it to [−<em>π,π</em>]<sup>2</sup>, and then sample at the integer locations. In this brief review, we shall adopt the former perspective, forming

where <em>g </em>(<strong>s</strong>) = (<em>f </em>∗ <em>h</em><sub>lpf</sub>)(<strong>s</strong>)

Since <em>f </em>(<strong>s</strong>) is just a linear combination of shifted copies of the <em>q </em>(<strong>s</strong>) interpolation kernel, we can obtain <em>g </em>= <em>f </em>∗ <em>h</em><sub>lpf </sub>by applying the low-pass anti-aliasing filter <em>h</em><sub>lpf </sub>directly to the interpolation kernel. That is,

<em>g </em>(<strong>s</strong>) = [<em>x</em>[<strong>n</strong>]<em>q</em><sub>lpf </sub>(<strong>s</strong>−<strong>n</strong>)

<strong>n</strong>

where

<em>q</em><sub>lpf </sub>(<strong>s</strong>) = (<em>q </em>∗ <em>h</em><sub>lpf</sub>)(<strong>s</strong>)

In our case, <em>q </em>(<strong>s</strong>) is ideally sinc(<em>s</em><sub>1</sub>)sinc(<em>s</em><sub>2</sub>), while <em>h</em><sub>lpf </sub>(<strong>s</strong>) is ideallyis equal to <em>h</em><sub>lpf </sub>(<strong>s</strong>).<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>It follows that

<strong>n</strong>

Again, the simplest and most efficient way to implement this is to perform the reduction separably (rows then columns, or vice-versa) and adopt the inner products (output-driven) perspective, as follows. For the even locations of the output, we get

That is, we take the inner product between the input sequence and a copy of <em>q</em><sub>lpf </sub>that is translated by 5<em>m </em>and sampled at the integer locations. Similarly, for the other locations of the output, we get

<em>,</em><strong>q</strong>lpf<em>,</em>

That is, we need three sets of discrete kernels,  and  that are obtained by sampling the stretched sinc function <em>q</em><sub>lpf</sub>, shifted by 0 and , then each output sample is obtained by translating the relevant kernel to the nearest integer location to the desired one —  is closest to integer location 5<em>m</em>+2, and  is closest to integer location 5<em>m</em>+3 — and taking the inner product between the input sequence and the shifted kernel.

It is worth sketching what is happening in the above equations on paper. In particular, you will find that everything is remarkably simple. All we are doing in every case is translating an appropriate sinc function so that it is centred at the location where we want to obtain an output sample, whereupon we take the inner product between the input samples and the samples of the translated sinc function.

Again, since sinc functions have infinite extent, you will need to window them or use some other approximation technique. Be sure to remember to use a window that has the same centre of symmetry as the underlying continuous sinc function that will be sampled.

<h1>3           How to go about the tasks</h1>

For demonstration purposes, you should create a separate project for each task, within a single workspace. You can do this by using the “File → New → Project” option in Visual C++, replacing the “Create new solution” option with “Add to solution”.

Be sure to arrange for all your executable programs to be placed in a single location (e.g., c:elec4642bin), which is referenced from the path environment variable. Also, please place all the images you are working with into a single directory (e.g., c:elec4642data). That way, it will be much easier to demonstrate your work in a time effective manner — it will also save you personally a lot of time. In any event, you will not be marked for your work unless it is organized in this way.

You should also rely much more heavily on viewing images using mi_viewer than the Windows previewer. There are lots of reasons for this, but the obvious ones are that you can launch multiple copies to view multiple images at once, and you can zoom in and inspect individual pixel values with ease. Ultimately, it is a great deal faster and less confusing to execute your programs and view your images directly from the DOS prompt, than by using mouse clicks — assuming you can type. Remember that the up and down arrows allow you to scroll through and edit previous commands easily. Remember also that you can use the tab key to expand file names, program names, etc., so you rarely have to type everything in full.

One thing you need to be prepared for is that it may be difficult at first to know whether your program is working correctly. This is a real world dilemma that you need to learn how to address. Just because your program compiles and produces something which looks like an image does not mean it is doing the right thing. So you need to have a good feeling for what the result should look like, based on your understanding of the fundamental concepts.

<ul>

 <li>To debug your program, you may find it useful to process very tiny images with only a few samples, so you can check that the output is as expected; you can use something like “mi_pipe2 -i image.bmp -o tiny.bmp -form bmp :: crop_n_shuffle -crop 0 0 16 16” to create a 16×16 image by cropping a much bigger one. You can also read the sample values directly within the “mi_viewer” application by zooming in (z accelerator) and holding the mouse button down.</li>

 <li>It is a good idea to rely upon the theory you have learned to verify your programs. For example, you know that a good resolution reduction algorithm should be shift invariant, or nearly so. If this is the case, you should not observe any kind of modulation or pattern in the output which did not exist in the input — pay special attention around oriented edges, which can be understood as a single feature that consistently shifts from row to row or column to column by a non-integer amount.</li>

 <li>You can also use apply your interpolation program to the output of your reduction program and vice-versa. In each case you will obtain an image of the same size as the original (or a little larger, in which case you can crop it down), so you can use the Media Interface tools to compare the resulting image with the original — this is a powerful way to check for any shift variance issues. One useful tool is the Media Interface “image_arith” module, which can be used to form the difference between two images as follows:</li>

</ul>

— mi_pipe2 -in1 image1.bmp -in2 image2.bmp :: image_arith -add 1 -1

It should be apparent that to verify your work, it may be helpful to collect sequences of command-line statements together into a script that you can run quickly. You can do this with a Windows batch file (i.e., any file with the “.bat” suffix), which can then be executed like any other program from the command-line. This will greatly facilitate the marking for demonstrators, who might insist upon it.

<h1>4           Tasks</h1>

Task 1: (5 marks + up to 3 possible bonus marks) Write a program which accepts a BMP image as input and writes out a reduced image, having dimensions. Notice that you should round the dimensions up to the nearest integer (ceiling function). Your program should be based upon the method of Section 2.2, with windowed sinc filters. You should adopt a Hanning window. Here are some additional specifics:

<ol>

 <li>Your program should accept a command-line argument <em>H </em>(loosely interpreted as the window half-length) such that the filter PSFs (or inner product kernels) <em>q</em><sub>lpf </sub>and all have their nonzero values falling within a (2<em>H </em>+ 1)×(2<em>H </em>+ 1) region of support. Your program should function correctly for values of <em>H </em>in the range 0 through to 14 at least, so that you can demonstrate the effect of different filter complexities. The special value <em>H </em>= 0 means, of course, that your inner products all involve exactly one coefficient — if your program is any good, this will be equivalent to nearest neighbour interpolation — check that it works correctly by looking at the numerical values with “mi_viewer.”</li>

 <li>Windowing may impact the DC gain of the filter somewhat. Be sure to renormalize your filters so that the DC gain is always 1 in this application.</li>

 <li>It is best to apply a continuous window function directly to the sinc function <em>q</em><sub>lpf </sub>(<em>s</em>), then shift and sample the windowed result, so that you do not accidentally introduce further shifts due to a window that has a different centre of mass to the sinc function. Draw sketches and figure out what continuous window parameter <em>τ </em>should be selected for a given value of the integer parameter <em>H </em>that your program accepts on the command-line.</li>

 <li>You are free to use either a floating-point or a fixed-point (integer) implementation, but you will probably find a floating-point implementation to be easier for this exercise.</li>

 <li>Remember to apply a suitable boundary extension method prior to filtering.</li>

 <li>Up to 3 bonus marks may be awarded for efficient implementation of this task. Ideally, your implementation should strive to perform as few computations as possible, while still correctly implementing the reduction filter for the specified window size. You should consider separability as an important basis for minimizing the complexity. You may also consider the option of fixed point processing with 16-bit sample and coefficient precisions. To gain these bonus marks you will need to demonstrate actual measured improvements in processing speed.</li>

</ol>

Task 2: (2 marks) Write a program which accepts a BMP image as input and writes out an expanded image, having  times the number of rows and columns. The new dimensions should be. Your program in this part should be based on bi-linear interpolation (see Chapter 3 of your lecture notes). Note that bi-linear interpolation is similar to sinc-interplation, in that you are finding the nearest location in the input image to the desired one, and taking the inner product of a small neighbourhood of samples around that location with the coefficients of a kernel that depends on the shift amount. In this case, however, the kernel only has 4 coefficients and the formula is very simple. You might like to go back over the Week 5 lecture to understand what it means to interpolate an image using the bi-linear kernel, rather than a sinc kernel. You should look for artefacts in the interpolated result that might reveal the effects of aliasing, even though it will not be a very strong effect for most inputs.

Task 3: (3 marks + up to 2 possible bonus marks) Write a program which accepts a BMP image as input and writes out an expanded image, having  times the number of rows and columns, as above. Your program in this part should be based on the method of Section 2.1, with windowed sincs. Here are some additional specifics:

<ol>

 <li>The half-window size parameter <em>H </em>should be a command-line argument to your program, so that you can demonstrate the effect of different filter lengths. The meaning of <em>H </em>is, as before, that all inner products (if implemented non-separably) should involve kernels with a region of support that is limited to (2<em>H </em>+ 1)×(2<em>H </em>+ 1). Your program must work for values of <em>H </em>in the range 0 through 7, where <em>H </em>= 0 again corresponds to nearest neighbour interpolation — if you have implemented things correctly, this does not need to be treated as a special case.</li>

 <li>Windowing may impact the DC gain of the interpolation kernels somewhat. Be sure to renormalize your coefficients so that the DC gain is always 1.</li>

 <li>Remember to apply a suitable boundary extension method prior to filtering.</li>

 <li>Up to 2 bonus marks may be awarded for efficient implementation of this task. Again, you should consider separability and you will need to demonstrate actual measured improvements in speed. You may also be rewarded for efficient memory utilization in the implementation of your expansion operator.</li>

</ol>

Task 4: (Up to three bonus marks) Write a program which accepts two BMP images <em>x</em>[<strong>n</strong>] and <em>y </em>[<strong>n</strong>] as input, generates a difference imageas output, and also computes the mean error and mean squared error (MSE) between the input images. If the images are each of size <em>M </em>×<em>N</em>, these quantities are

Mean Error

and

MSE

From the MSE, you should derive the peak signal to noise ratio (PSNR), which is defined by

255<sup>2</sup>

PSNR = 10log<sub>10 </sub>

MSE

for 8-bit sample values.

Note: While this task is simple, you will need to deal with images that do not have identical dimensions to perform all the investigations below. Where the input images have different dimensions, your program should ignore extra rows on the bottom or columns on the right of the larger image.

<ol>

 <li>1 bonus mark: Arrange for your program to print the Mean Error, MSE and PSNR for each colour plane, as well as generating the difference image.</li>

 <li>1 bonus mark: Use your program to investigate the difference between an original image and one obtained by first expanding it and then reducing it back to its original size. Compare the performance of the bi-linear and windowed sinc expansion algorithms represented by Tasks 2 and 3. Also, determine the impact of the window sizes. To receive these bonus marks you must be prepared to explain what you observe.</li>

 <li>1 bonus mark: Use your program to investigate the difference between an original image and one obtained by first reducing it and then expanding it back to its original size. Again, study the effect of window size and bi-linear vs. windowed sinc expansion. To receive these bonus marks you must be prepared to explain what you observe.</li>

</ol>

<h1>5           Assessment</h1>

You should not rely upon implementing this project within the scheduled laboratory sessions. You will have an opportunity to do this in the Week 7 and even Week 9 laboratory sessions, but it is recommended that you aim to have some of the project implemented prior to Week 7 so that you can get feedback from the demonstrators prior to final assessment. In any event, you must be prepared to demonstrate your work by Week 9 and be assessed individually at any time during the last two hours of that session — you cannot expect demonstrators to mark your work only during the final hour of the laboratory session.

In order to obtain the full marks for any given task, you must have a working program to demonstrate and you must be able to explain how it works and answer questions very quickly!! We will not be able to wait around while you fiddle with running your program from the debugger or viewing images with the Windows previewer, so have things set up for easy validation by the course staff and use “mi_viewer” to show your images directly from the command-line, allowing multiple images to be viewed simultaneously and individual pixel values to be read-off by the marker with simple mouse clicks.

You may feel free to re-use code from Labs 1 to 3, so long as you understand it. You may also discuss the project with other students in the class, but your programs should be your own original work. As with assessment tasks in other subjects, UNSW regards plagiarism as a serious offense; for a first offense, the penalty would be loss of all marks for the project.

<a href="#_ftnref1" name="_ftn1">[1]</a> If this is not obvious to you, consider what happens in the Fourier domain. <em>q</em>ˆ(<em>π,π</em>]<sup>2 </sup>and 0 elsewhere. <em>h</em>ˆ<sub>lpf </sub>(ω) is equal to 1 inside [−3<em>π/</em>5<em>,</em>3<em>π/</em>5]<sup>2 </sup>and 0 elsewhere. Consequently,