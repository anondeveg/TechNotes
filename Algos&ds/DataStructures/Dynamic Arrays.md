
- dynamic arrays are arrays that are not limited by a specific size during initialization 

- they are the default in python,javascript, and others

## initialize, fill, expand

dynamic arrays gets initialized with a default size (language dependent) then filled like a normal static array, but when the default size gets completely filled if you try to append any item to it it will make a completely new array in a different  memory location with added size or example the new array might be (Default size + n) where n is a language dependent variable that is not equal to one -because if it was then we will have to initialize a new array every time we append a new value making it horribly inefficient. after creating the new array we will transfer values from the old filled array to the new one then delete the old one.


## expansion operation time complexity

when we have to move to a new array we have to do 2 things

first allocate a new array with the size limit O(n)
then transfer all the items from the first array to the new array O(n)

which makes this whole operation time complexity O(2n)
in big o notation we ignore constants so it will be 
O(n)

if we had to allocate a new array each time we wanted to insert an item that will make the insert operation o(n) instead of the static array insert time complexity of o(1) 

to fix this we allocate more than we actually need each time we expand to a new array 

for example if we said the first array size was 5 then when we try to append the sixth item it will create a new array with size 10 instead of 6 to avoid creating a new array when inserting the 7th,8th,9th,10th item 

this trick makes the [amortized time complexity]  of the operation O(n) 