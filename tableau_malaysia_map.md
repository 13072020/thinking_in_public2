
Make a Tableau map of Malaysia, by titles of head of state.

Malaysia has:

- 9 monarchies, known as Malay States (not including the federal monarchy)
- 3 federal territories
- 4 non-monarchical states

Tableau can show us their distribution.

https://en.wikipedia.org/wiki/States_and_federal_territories_of_Malaysia

# Tableau mapping instructions

1. Read in the CSV
2. In Data Source tab, click on "Abc" or the globe logo, and make sure 'Jurisdiction' is a 'String' with a 'Geographic Role' of 'State/Province', and 'Head of state' is a 'String' with no geographic role
3. 'Go to Worksheet' / click 'Sheet 1'
4. Drag Latitude into Rows bar, and Longitude into Columns bar
5. Drag Jurisdiction (NOT Head of state) into the bottom of the Marks panel
6. Click on '16 unknown' in bottom right, this brings up 'Edit Locations'
7. Change Country/Region from Australia to Malaysia
8. Match 'Malacca' to 'Melaka'
9. Drag Head of State onto 'Color' in Marks panel

# The data

Jurisdiction|Head of state
---|---
Kuala Lumpur|Yang di-Pertuan Agong
Labuan|Yang di-Pertuan Agong
Putrajaya|Yang di-Pertuan Agong
Johor|Sultan
Kedah|Sultan
Kelantan|Sultan
Pahang|Sultan
Perak|Sultan
Selangor|Sultan
Malacca|Yang di-Pertua Negeri
Penang|Yang di-Pertua Negeri
Sabah|Yang di-Pertua Negeri
Sarawak|Yang di-Pertua Negeri
Perlis|Raja
Negeri Sembilan|Yang di-Pertuan Besar
