---
layout: post
title: "Dabbling in VBA"
date: 2019-01-20 19:13:15 +0000
permalink: dabbling_in_vba
---

If you have read my first blog you know that my journey into programming started with excel and VBA (Visual Basic for Application). 

Of coarse I automated several thing but for this blog I am going to talk about billing, which really just needed to be simplified.

When I arrived it was a pile of past bill that you sifted through to find the acct number along with two different pieces of paper with rows of codes. So first things first, I added it all to a spreadsheet.

After that I added a couple list box and an indirect list box and wrote "VLOOKUP" formulas to find the value for the account and category.

But I wanted it to be easier; because why not?
The work flow is simple. Select and account, then select a category within that account and ultimately you write the associated code on the bill and in some instances, the bill is spit into two categories so sometimes some calculation is needed. 

This week I found myself reinventing this simple sheet. to make it more intuitive. The basic layout of the forms is as follows. 

**1st Form - Select account**
Acc1 Acct2 Acct3
Acc4 Acct5 Acct6

Form Code
```
Private Sub A_Click()
    Call UpdateAcct("A")
End Sub

Private Sub B_Click()
    Call UpdateAcct("B")
End Sub

Private Sub S_Click()
    UserFormSearch.Show
End Sub

Private Sub UserForm_Terminate()
    Application.Quit
End Sub
```

Module Code
```
Sub UpdateAcct(Acct)
    Range("B2") = Acct
    Dim FormName As String
    FormName = "UserForm" & Left(Acct, 1)
    Dim DForm As Object
    Set DForm = UserFormS.Add(FormName)
    DForm.Show
End Sub
```

**2nd Form - Selected account's categories**
Cat1 Cat2 Cat3
Cat4 Cat5 Cat6
Cat7 Cat8 Cat9

Form Code
```
Private Sub a1_Click()
    Call UpdateCat("A1")
End Sub

Private Sub a2_Click()
    Call UpdateCat("A2")
End Sub

Private Sub UserForm_Terminate()
    Range("B2") = ""
End Sub
```

Module Code
```
Sub UpdateCat(Cat)
    Range("B4") = Cat
    Item = Range("B2") & Cat
    If Cat = "Engineering" Or Cat = "Security" Then
        UserFormCal.Show
    Else
        UserFormR.Show
    End If
End Sub
```

**3rd Form - Calculation (Shows only if bill should a split)**
Amount1
Amount2 (show only if bill is not split in half)

Form Code
```
Private Sub UserForm_Initialize()
    Range("C2") = ""
    Range("C4") = ""
    Me.Lab2.Caption = Range("C3")
    If Range("B4") = "Engineering" Then
        Lab2.Visible = False
        Num2.Visible = False
    End If
End Sub

Private Sub Submit_Click()
    Range("C2") = Num1.Value
    Range("C4") = Num2.Value
    Unload Me
    UserFormR.Show
End Sub
```

**4th Form - Result page**
Acct#1
Acct#2 (shows if split)

Form Code
```
Private Sub UserForm_Initialize()
    AL1.Caption = Range("A6")
    AN1.Caption = Range("B6")
    AN2.Caption = Range("C6")
    BL1.Caption = Range("A7")
    BN1.Caption = Range("B7")
    BN2.Caption = Range("C7")
    If Range("C2") = "" Then
        AL2.Visible = False
        AN2.Visible = False
        BL2.Visible = False
        BN2.Visible = False
    End If
    AN2 = Format(AN2, "$0.00")
    BN2 = Format(BN2, "$0.00")
End Sub

Private Sub UserForm_Terminate()
    Range("B4") = ""
    Range("C2") = ""
    Range("C4") = ""
End Sub
```

I also wanted it to be as intuitive as possible, so if you have several bills with in the same account, the 2nd form stays opened until closed, this way you can move through them very quickly.

I also left the original sheet in contact so that either one can be used for whatever reason. 

Finally I added a search field based on the business name.
This was one of the most fun parts because I want to find the acct and category and then just reveal the end acct#, which is what I did. But in some instances the business may have multiple categories and if that be the case, it opens up to the category form. 


Form Code
```
Private Sub Biz_Change()
    Range("B15") = Biz.Value
    Range("B2") = Range("B16")
    Range("B4") = Range("B17")
    If Range("B17") <> "0" Then
        UserFormR.Show
        Unload Me
    Else
        Dim FormName As String
        FormName = "UserForm" & Left(Range("B2"), 1)
        Dim DForm As Object
        Set DForm = UserFormS.Add(FormName)
        DForm.Show
    End If
End Sub
```

*And suddenly, it was beautiful in appearance and functionality.* 
