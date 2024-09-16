## Week 3
## Week of 09/015/2024

### Grasshopper 
Yes, I did it! This is my first time experimenting with Grasshopper, and I can’t imagine that such a simple design has so much complexity behind it. In the beginning, I don’t quite understand why I need Grasshopper, especially since I can design this phone stand in NX in 10 minutes and probably have it printed before I'm done with Grasshopper. However, as I spend more time working with it and gain more understanding, I’m starting to appreciate and enjoy the process.

<img width="1919" alt="image" src="https://github.com/user-attachments/assets/9ccede1f-976b-4b8c-b7ce-1ce6290d244b">

Now, let's talk about the phone stand design. My requirements are very specific—I need a stand that allows me to set the phone vertically at different angles. I don’t like designs with an adjustable backplate; I’ve seen a few, and they tend to be flimsy and difficult to print. What I’m looking for is something simple and sturdy.

| ![image](https://github.com/user-attachments/assets/d2124669-8ff4-4fa0-a24d-995bcbf8269d)| ![image2](https://github.com/user-attachments/assets/6f9119c7-f531-43bf-9505-cee432dbc8f5)|
|:---:|:---:|
| NO | NO |

I own a Samsung Galaxy S24 Ultra. After searching for its dimensions online, I decide to first create a phone model in Grasshopper with the same dimensions as mine, and then design the stand around it. I create the phone model using the`Rectangle` function first, then convert it to a Brep with extrusion. I use the `Move` function in both X and Y directions, and the `Rotate` function to adjust the viewing angle.

<img width="1919" alt="image" src="https://github.com/user-attachments/assets/829fd73e-a817-4798-b7fa-35fee9ea9341">

At this point, I'm thinking, why not just create multiple slots in the phone stand at different tilt angles? That way, I can easily place the phone in whichever slot suits my needs, providing various viewing angles without the need for complex mechanisms. Plus, it would be very easy to print since no support is needed.

<img width="1919" alt="image" src="https://github.com/user-attachments/assets/141e513f-75ee-4a38-b479-9df1e9c79749">

What I’m doing here is simply copying the workflow for the first phone three times, so now I have a total of four slots that provide four different viewing angles. However, there’s a slight problem: since the code is a direct copy, I have to adjust the phone’s location on the X and Y axes, as well as the rotation angle, individually for each one. This turns out to be a lot of work, and since I’m a lazy person, I’m creating a calculation workflow to automatically set the distance between the phones and the tilt angle offset.

| ![image](https://github.com/user-attachments/assets/7be976e1-fc24-4f09-b133-de3c454f48ae) | ![image2](https://github.com/user-attachments/assets/469b96a3-8fb2-4edf-9fea-c858e6910227) |
|:---:|:---:|
| 6MM | 19MM |

| ![image](https://github.com/user-attachments/assets/316838c2-72f3-42a8-8932-3115c7d33cb9) | ![image2](https://github.com/user-attachments/assets/7b0a375a-c046-4363-b996-f84c9f839bde) |
|:---:|:---:|
| 0 DEGREE | 15 DEGREE  |



