## Week 3
## Week of 09/015/2024

### Grasshopper 
Yes, I did it! This is my first time experimenting with Grasshopper, and I could never have imagined that such a simple design could have so much complexity behind it. In the beginning, I didn’t quite understand why I needed Grasshopper, especially since I could design this phone stand in NX in 10 minutes and probably have it printed out before I'm done with Grasshopper. However, after spending some time working with it and gaining more understanding, I’ve started to appreciate and enjoy the process.

<img width="1919" alt="image" src="https://github.com/user-attachments/assets/9ccede1f-976b-4b8c-b7ce-1ce6290d244b">

Now, let's talk about the phone stand design. My requirements are very specific—I need a stand that allows me to set the phone vertically at different angles. I don’t like designs with an adjustable backplate; I’ve seen a few, and they tend to be flimsy and difficult to print. What I want is something simple and sturdy.

| ![image](https://github.com/user-attachments/assets/d2124669-8ff4-4fa0-a24d-995bcbf8269d)| ![image2](https://github.com/user-attachments/assets/6f9119c7-f531-43bf-9505-cee432dbc8f5)|
|:---:|:---:|
| NO | NO |

I own a Samsung Galaxy S24 Ultra. After searching for its dimensions online, I decided to first create a phone model in Grasshopper with the same dimensions as mine, and then design the stand around it. The phone model was created using the `Rectangle` function first, then converted to a Brep with extrusion. I used the `Move` function in both X and Y directions, and the `Rotate` function to adjust the viewing angle.

<img width="1919" alt="image" src="https://github.com/user-attachments/assets/829fd73e-a817-4798-b7fa-35fee9ea9341">

At this point, I'm thinking, why not just create multiple slots in the phone stand at different tilt angles? That way, I can easily place the phone in whichever slot suits my needs, providing various viewing angles without the need for complex mechanisms. Plus, it would be very easy to print since no support is needed.


