# multiLevelUITableView
a multilevel table view example showing the tree with its branches

# SCREENSHOT
![Alt text](https://github.com/deepakbhati99/multiLevelUITableView/blob/master/giphy.gif "Screeshot #1")

#pragma mark - Table View Delegate
    -(void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath{
    
    NSDictionary *dic = _objects[indexPath.row];
    
    NSInteger indentLevel = [_objects[indexPath.row][keyIndent] integerValue]; //indentLevel of the selected cell
    NSArray *indentArray = [_objects valueForKey:keyIndent]; //array of indents which are currently show on table
    
    BOOL indentChek = [indentArray containsObject:[NSNumber numberWithInteger:indentLevel]]; // check if  selected
    BOOL isChildrenAlreadyInserted = [_objects containsObject:dic[keyChildren]]; //checking contains children
    
    
    for(NSDictionary *dicChildren in dic[keyChildren]){
        
        NSInteger index=[_objects indexOfObjectIdenticalTo:dicChildren];
        
        isChildrenAlreadyInserted=(index>0 && index!=NSIntegerMax); //checking contains children
        
        if(isChildrenAlreadyInserted) break;
        
    }
    
    
    if ( indentChek &&  isChildrenAlreadyInserted) {
        
        //all children from this category will be deleted
        [self miniMizeThisRows:_objects[indexPath.row][keyChildren] forTable:tableView withIndexpath:indexPath];
        
    }
    else if ([dic[keyChildren] count]) { //insert the children if contains
        
        NSMutableArray *ipsArray = [NSMutableArray new];
        NSArray *childArray = dic[keyChildren];
        
        NSInteger count = indexPath.row + 1;
        
        for (int i = 0; i < [dic[keyChildren] count]; i++,count++) {
            
            NSIndexPath *ip = [NSIndexPath indexPathForRow:count inSection:indexPath.section];
            [ipsArray addObject:ip];
            [_objects insertObject:childArray[i] atIndex:count];
            
        }
        
        [self.tableView beginUpdates];
        [self.tableView insertRowsAtIndexPaths:ipsArray withRowAnimation:UITableViewRowAnimationLeft];
        [self.tableView endUpdates];
        
        
    }else{ //the junior most children will navigate
        
        [self performSegueWithIdentifier:@"showDetail" sender:indexPath];
    }
    
    
    }

/*this method will check the category with its children, sub children and so on till the last indent occurs*/
//method to minimize all the child rows of that particular category
    -(void)miniMizeThisRows:(NSArray*)ar forTable:(UITableView *)tableView withIndexpath:(NSIndexPath *)indexPath{
    
    for(NSDictionary *dicChildren in ar ) {
        
        NSUInteger indexToRemove=[_objects indexOfObjectIdenticalTo:dicChildren];
        
        NSArray *arrayChildren=[dicChildren valueForKey:keyChildren];
        
        if(arrayChildren && [arrayChildren count]>0){
            [self miniMizeThisRows:arrayChildren forTable:tableView withIndexpath:indexPath];//calling self method to remove  the childrens
        }
        
        if([_objects indexOfObjectIdenticalTo:dicChildren]!=NSNotFound) {
            
            //updating array
            [_objects removeObjectIdenticalTo:dicChildren];
            
            //deleting the row
            [tableView deleteRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:indexToRemove inSection:indexPath.section]] withRowAnimation:UITableViewRowAnimationRight];
        }
    }
    }

