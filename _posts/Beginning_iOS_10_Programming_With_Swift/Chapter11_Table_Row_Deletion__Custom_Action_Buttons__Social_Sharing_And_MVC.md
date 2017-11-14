# Chapter 11 Table Row Deletion, Custom Action Buttons, Social Sharing and MVC

***To Do***:

1. *Swipe for More* Action
2. Social Sharing Feature

### Understanding Model-View-Controller

The *model-view-controller(MVC)* is an example of *Separation of Concerns(Soc)*.

+ **Model**: holding/operations on data
+ **View**: visual display of information
+ **Controller**: translate user interaction from the view into the action to be performed in the model.

### Deleting a Row from UITableView

Method `tableView(_:commit:forRowAt:)` in `UITableViewDataSource` protocol handle the deletion (or insertion) of a specific row.

Implement the `tableView(_:commit:forRowAt:)`, the table view will automatically display a *Delete* button when the user swipe across a row.

	override func tableView(_ tableView: UITableView, commit editingStyle:
	UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {

	}	

### Delete Row Data from the Model

To remove an item from an array, we call the `remove(at:)` method.


	override func tableView(_ tableView: UITableView, commit editingStyle:
	UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
	
		if editingStyle == .delete {
			// Delete the row from the data source
		    restaurantNames.remove(at: indexPath.row)
		    restaurantLocations.remove(at: indexPath.row)
		    restaurantTypes.remove(at: indexPath.row)
		    restaurantIsVisited.remove(at: indexPath.row)
		    restaurantImages.remove(at: indexPath.row)
		}
	}

The method support two type of editing styles: *insert* and *delete*.

### Reloading UITableView

Calling `reloadData` method.

	UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
	    if editingStyle == .delete {
	        // Delete the row from the data source
	        restaurantNames.remove(at: indexPath.row)
	        restaurantLocations.remove(at: indexPath.row)
	        restaurantTypes.remove(at: indexPath.row)
	        restaurantIsVisited.remove(at: indexPath.row)
	        restaurantImages.remove(at: indexPath.row)
	    }

	    tableView.reloadData()

		print( "Total item: \(restaurantNames.count)")
	    for name in restaurantNames {
	        print(name)
	    }
	}

---

***Tips***

Print out the content:

	print("Total item: \(restaurantNames.count)")
	for name in restaurantNames {
   		print(name)
	}

By default, the console is hidden in Xcode. Select *View > Debug Area > Activate Console*
