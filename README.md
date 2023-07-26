Imports System.IO
Imports System.Windows.Forms

Public Class PawanGovindanaik

    Private OpenFileDialog As New OpenFileDialog()
    Private SaveFileDialog As New SaveFileDialog()
    Private MinPosition As Integer = 0 ' Default minimum position is set to 0
    Private MaxPosition As Integer = 1000 ' Default maximum position is set to 1000

    Private axisMover As New AxisMove() ' Create an instance of the AxisMove class
    Private targetValue As Integer = 100 ' Set the targetValue for the AxisMove

    Private Sub PawanGovindanaik_Load(sender As Object, e As EventArgs) Handles MyBase.Load
        ' Display the default minimum and maximum position values on the corresponding labels
        Lbl_MinSpeedData.Text = MinPosition.ToString()
        Lbl_MaxSpeedData.Text = MaxPosition.ToString()

        ' Set a default speed (you can change this based on your requirement)
        axisMover.SetSpeed(50)
        Lbl_SpeedData.Text = axisMover.GetSpeed().ToString()

        ' Subscribe to the IncrementingValueChanged event of the AxisMove class
        AddHandler axisMover.IncrementingValueChanged, AddressOf IncrementorIncrementingValueChanged


        axisMover.SetTargetPos(targetValue)
    End Sub


    Private Sub Btn_AbsoluteMov_Click(sender As Object, e As EventArgs) Handles Btn_AbsoluteMov.Click
        ' Display an input box to let the user enter the value (between MinPosition and MaxPosition)
        Dim inputPrompt As String = "Enter a value (" & MinPosition.ToString() & "-" & MaxPosition.ToString() & "):"
        Dim inputValue As String = InputBox(inputPrompt, "Enter Value")

        Dim parsedValue As Integer

        If Integer.TryParse(inputValue, parsedValue) AndAlso parsedValue >= MinPosition AndAlso parsedValue <= MaxPosition Then
            axisMover.MoveAbsolute(parsedValue, MinPosition, MaxPosition)
        Else
            MessageBox.Show("Invalid input! Please enter a valid integer between " & MinPosition.ToString() & " and " & MaxPosition.ToString() & ".", "Error")
        End If
    End Sub

    Private Sub Btn_Limits_Click(sender As Object, e As EventArgs) Handles Btn_Limits.Click
        axisMover.SetLimits(Me, MinPosition, MaxPosition)
    End Sub

    Private Sub Btn_Speed_Click(sender As Object, e As EventArgs) Handles Btn_Speed.Click
        axisMover.SetSpeed(Me)
    End Sub


    Private Sub Btn_RelativeMov_Click(sender As Object, e As EventArgs) Handles Btn_RelativeMov.Click
        ' Display an input box to let the user enter the value for MovRelative
        Dim inputPrompt As String = "Enter a relative value to add or reduce from the current target position:"
        Dim inputValue As String = InputBox(inputPrompt, "Enter Relative Value")

        ' Check if the user entered a valid integer value
        Dim relativeValue As Integer

        If Integer.TryParse(inputValue, relativeValue) Then
            axisMover.MoveRelative(Me, relativeValue, MinPosition, MaxPosition)
        Else
            MessageBox.Show("Invalid input! Please enter a valid integer for relative movement.", "Error")
        End If
    End Sub
    Private Sub IncrementorIncrementingValueChanged(ByVal newValue As Integer)
        ' Update lblPosMove with the current value during each incrementing step
        Lbl_MoveSpeed.Text = newValue.ToString()
    End Sub

    Private Sub Btn_LoadProg_Click(sender As Object, e As EventArgs) Handles Btn_LoadProg.Click
        OpenFileDialog.Filter = "Text Files (*.txt)|*.txt|All Files (*.*)|*.*"
        OpenFileDialog.FilterIndex = 1

        If OpenFileDialog.ShowDialog() = DialogResult.OK Then
            ' Read the contents of the selected file
            Dim selectedFilePath As String = OpenFileDialog.FileName
            Dim fileContent As String = File.ReadAllText(selectedFilePath)


            LblProgList.Text = fileContent
        End If
    End Sub

    Private Sub Btn_SaveProg_Click(sender As Object, e As EventArgs) Handles Btn_SaveProg.Click
        SaveFileDialog.Filter = "Text Files (*.rob)|*.rob|All Files (*.*)|*.*"
        SaveFileDialog.FilterIndex = 1

        If SaveFileDialog.ShowDialog() = DialogResult.OK Then
            ' Get the selected save file path
            Dim selectedFilePath As String = SaveFileDialog.FileName

            ' Save the contents of the RichTextBox to the selected file
            File.WriteAllText(selectedFilePath, LblProgList.Text)

            MessageBox.Show("File saved successfully.", "Success")
        End If
    End Sub


End Class
