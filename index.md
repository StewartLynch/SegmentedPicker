# Cloning a Swift Package and Modifying It

There are a few reasons why you would want to clone a Swift Package and claim it as your own so that you can modify it.

You may wish to simply pick through the code so you can get a better understanding of how things are done and improve your skills.  I often do this by altering the code and seeing how it affects things. Or, you may wish to improve upon the package for your own purposes.

When you use Swift Package manager to install a Swift Package, you can inspect the code, but you can't make any modifications to it.

In this article, you will learn how to clone a Swift Package from a GitHub repo and add it to your own project as a local framework so that you can modify it.

> **Caution:** If you are going to be cloning someone else's work, altering and using it for your own purposes, make sure that you read the license file to ensure that you are allowed to do that and identify any requirements there might be for giving appropriate credit

### The source code

For this exercise, I am going to use code that was produced by *Frank Jia* in his article titled **Build a Custom iOS Segmented Control With SwiftUI** published on **Medium** at https://betterprogramming.pub/custom-ios-segmented-control-with-swiftui-473b386d0b51

I have made only minor modifications to the code to make it compatible as a Swift Package, and to add an accessibility property.  Otherwise, I make no claim to the code, I am using it only as an example for my tutorial.

### The Swift Package

The swift package I created can be found at https://github.com/StewartLynch/SegmentedPicker but more on that shortly.

### Create A Test Project

For this exercise, create a Swift project using SwiftUI and call it **SegmentedPickerClone**

![image-20211006212909857](Images/image-20211006212909857.png)

We are going to be replacing the standard Segmented Picker that comes out of the box in SwiftUI with the custom one that Frank creates in his artcle.  Then we are going to clone it and install it as a local framework and make some modifications.

#### The SwiftUI Segemented Picker

The standard SwiftU picker of the type SegmentedPickerStyle can be created using the SwiftUI Picker constructor that requires a Title, and selection bound to some value and an array of strings or views that you can use for the segment labels.

For example, you can create a @State property like this for selection

`````swift
@State private var selection: Int = 0
`````

The array of items could be an array of strings representing colors like this:

`````swift
var colors = ["Red", "Green", "Yellow"]
`````

Then we replace that Hello World TextView with a Picker that is a segmented picker like this:

`````swift
Picker("Pick your favorite color", selection: $selection) {
    ForEach(0..<colors.count, id: \.self) { index in
        Text(colors[index]).tag(index)
    }
}
.pickerStyle(.segmented)
`````

Frank's custom segmented picker provides us with a much easier syntax and it also opens up some real possibilities for our own customizations.

So let's get started

### Installing the Swift Package

Installing Swift Packages is extremely easy compared to installing with CocoaPods.   This is all you have to do

1. Copy the URL from the repository (https://github.com/StewartLynch/SegmentedPicker)
2. In Xcode, from the File menu, choose Add Packages...
3. In the search field, paste the copied URL.  If the URL is recognized, it will be displayed along with the Dependency Rule.
4. Click on **Add Package**
5. When the Next screen appears, click on **Add Package** once more.

That's it.  You have installed the package

##### Inspect the Package

If you want to inspect the package contents, drill oen on the **Package Dependencies** until you reach the      **Sources/\<PackageName>** folder and inside there, you will find the code for the package. 

We will come back to this later when we need to modify the code.

##### The Framework

The package is installed as a **framework** and this means that it is in a separate module so eveything that need to access from your project must be **public.**

If you select your project target, and scroll to the **Frameworks, Libraries, and Embedded Content** section you will see that the framework has been added to your project.

Now it is time to use it.

### Using the Package

Wherever you wish to use the package, you need to import it.  So in ContentView, we want to replace the Picker we have right now with that new custom one called **SegmentedPicker**.  So in ContentView, add

`````swift
import SegmentedPicker
`````

Replace the entire Picker you have in the body now (including the pickerStyle modifier) with an instance of SegmentedPicker

When you do this, you see that it has two arguments, an array of string and a binding to an int.

Well that is what we have used already, so

`````swift
SegmentedPicker("Pick your favorite color", items: colors, selection: $selection)
`````

This is a much nicer implementation and the segmented picker is arguably nicer too.

What I would like to do is to customize that picker by allow the user to specify the color that they wish to use as the background behind the segments.

If we add a background modifier to our Picker right now, it just does not work

`````swift
SegmentedPicker("Pick your favorite color", items: colors, selection: $selection)
    .background(Color.red)
`````

![image-20211006220541840](Images/image-20211006220541840.png)

The background of this picker (and a regular picker too) is the background of the entire view so this doesn't work.

If you read Frank's article you will see how he created this custom picker.  If we drill down to the secmentedPicker source, we can see that there is a private static property called BackgroundColor that is assigned as the background of the custom view.

What we want to be able to do is to modify that color so it can't be static.  So let's change that.

**Oops...... You can't**

Swift Packages are not modifyable.  You need to "own" the framework and that is what the whole point of this tutorial is all about.

### Clone the Package and add locally

You could go to the GitHub reposit and clone the package and add it to your project as a local framework, but there is a fast and easy way to do this.

1. Right-click on the package dependency and choose **Show in Finder**.  This will open up the finder to the location where the package has been added.  It is a pretty deep dive, but don't worry about that. 

2. Right-click on the selected folder and copy it to your clipboard.

3. Back in Xcode, right-click on the Project name at the top of the project navigator and choose **Show in Finder** and this will open the finder to the root level of your project.

4. Paste in the copied folder from step 2.

5. Return to Xcode and **Remove the Swift Package**.  To remove the package, you

   1. Select your project in the project navigator and then click on the project
   2. Click on the **Package Dependencies** tab to view all dependencies
   3. Click on the one you want to remove and then click on "-"

   ![image-20211006222207071](Images/image-20211006222207071.png)

6. Drag and drop the folder from the project root (step 4) into your project navigator

![image-20211006222736355](Images/image-20211006222736355.png)

This will add the project in as a framework, but you still need to add the framework to your target.

7. Select your project target and select your project target, and scroll to the **Frameworks, Libraries, and Embedded Content** section .
8. Click on the **+** and the list of frameworks and libraries will be displayed.  Select your new framework from the workspace section and click on Add and this will add it to your target

![image-20211006223104987](Images/image-20211006223104987.png)

You should now be able to build an run your project just as before.  The difference is that now, you will be able to modify the code.

### Modifying the Code

Now, if you are comfortable in modifying code in a separate module, then there is no need to continue on with this tutorial.  However, what I am going to do next is show you two different ways in which you can modify that background of the picker.  

First, remove the modifier on the SegmentedPicker if you still have it there.  We will add it back later.

#### Property Method

The first way that you can do this is by making tha backgroundColor property in the SegmentedPicker code available to be modified (optionally anyway) from the project

1. Open the SegmentedPicker from the Sources/SegmentedPicker folder in the framework.
2. Change 

`````swift
private static let BackgroundColor: Color = Color(.secondarySystemBackground)
TO
var backgroundColor: Color
`````

The first time you edit the file, you will be asked to unlock it.  What we are doing is replacing the static property with a variable one.  However, this needs to be publicly accessible, so we need to modify the initializer for the struct and while there, provide a default color in case the user wishes to use the default color.

3. Change the initializer to the following:

`````swift
public init(_ label: String, items: [String], selection: Binding<Int>, backgroundColor: Color = Color(.secondarySystemBackground)) {
  			self.label = label
        self._selection = selection
        self.items = items
        self.backgroundColor = backgroundColor
    }
`````

4. Replace the background property of the body 

`````swift
from
.background(SegmentedPicker.BackgroundColor)
to
.background(backgroundColor)
`````

##### Test

Return to contentView and if you build, you will see that nothing has changed.  However, if you type a comma after the second argument in SelectedPicker, you now see that you can add a third argument and that is backgroundColor so do that and pick a different color.

`````swift
SegmentedPicker(items: colors, selection: $selection, backgroundColor: .green)
`````

#### Modifier Method

The first method is not very "SwiftUI" in that in SwiftUI, you are less likely to pass additional arguments into a View, but rather apply a modifier like "background" that can modify and update the view.

So let's undo everything that you have done in that file
