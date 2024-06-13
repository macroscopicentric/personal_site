---
title:  "What is Object-Oriented Programming?"
date:   2016-01-23 16:17:00
categories: intro_to_programming
---

Object-oriented programming is the idea that data has its own identity and methods so it can do things for itself, instead of a workflow where functions do things _to_ the data. The main goal here is delegation: you can tell an object what you want it to do, but you don't have to worry about the details of *how* it will accomplish what you want. This allows you to decouple (remove unnecessary dependencies) your code. Decoupling your code means you can change the implementation of an object's method without needing to change the code calling that method, and vice versa. (If you found this explanation confusing, here's a [Quora post](https://www.quora.com/What-is-object-oriented-programming?share=1) that tries to answer the same question.)

In practice, object-oriented programming works by creating classes that have variables and methods, then creating instances of that class that store different information in the variables but have similar ways of interacting with it through the methods. You can have a lot of instances of the same class, so a common metaphor is to explain a class as a rubber stamp, and instances of that class like the images created by the rubber stamp. I like to compare classes to the Platonic ideal.* A class is a Platonic ideal of an object, and instances are actual working examples of the class. Like the Platonic ideal of a thing, a class is something you don't usually interact with directly. Instead, you make and then interact with instances of it. So if we use the example of a horse (a common example when explaining the Platonic ideal), you'd have a class Horse, and then individual horse instances of Horse that contain slightly different data, or ideas of what a horse can be. Perhaps your Horse class contains variables for things like age, gender, color or breed, height, etc. Then you could have different instances of Horse with different configurations, such as "a pinto mare" or "a bay yearling" or "a grey stallion." All these individual horses are still instances of Horse, so they all know how to do things that all horses should be able to do, like run. Putting all this together, we could write an example class and instance like this:

```py
	class Horse(object):

		def __init__(self, age, gender, color):
			self.age = age
			self.gender = gender
			self.color = color

		def run(self, distance):
			print "The %s horse runs %d miles." % (self.color, distance)

	pinto_mare = Horse(6, 'female', 'pinto')
	pinto_mare.run(4) # "The pinto horse runs 4 miles."
```

In a real program, you'd probably have a lot more than just one Horse instance. All instances of Horse would know how to run, and possibly jump, and eat, and do other things. Then instead of needing to manually move the horse (which involves knowing a) how to move a horse and b) how to move _this specific_ horse), you'd simply type `pinto_mare.run(4)` or `pinto_mare.walk(1)` and the horse would move itself.

Not everything needs its own class though! How do you tell when something is large enough to warrant writing a class for it? One good marker is trying to pass around a cluster of data all together and/or doing repetitive things to it. These actions signal that you're thinking of that data as a unified entity, at which point it might be worthwhile creating a separate class for it.

Writing object-oriented code allows you to decouple your code, which makes it more flexible, since you can change something without needing to also change everything that uses it. This makes both testing and editing your code much easier and less error-prone. I mean, who _doesn't_ want to make oodles of different horses?

> Then I decided to try an experiment and put all of my hard work to the test. The next morning I got up and walked straight to my computer..
(@Horse_ebooks)

*_In a nutshell, Plato was convinced that for every concept in the world, there was an ideal form of it that existed somewhere out in the ether. This perfect, ideal form appears in our minds when we try to categorize something we see in the real world. Ex: you see a horse. Your mind contains the Platonic ideal for Horse, and your mind then matches the real, existing horse to your mental Horse, and that's how you know that what you're seeing is indeed a horse._
