### Testing in go.

Why test? 
So you will not be like me who wrote an entire app for a client and had to depend on `Postman` and the `frontend` to help me test the app. I had to wait till it was testable
by those two before testing. Do I need to tell you that it was a failure?
Test so you can start finxing bugs early. Test because, although Popper says you cannot prove a thing entirely by experiment, you can falsify any scientific proposition.
If your attempt to falsify it proves correct, then you know you need to trash the proposition. It applies in software too. Testin does not prove correctness, only proofs do that.
But testing helps us ensure that under such conditions as were defined in a test, our program produces the desired output. It think most tests are about `pass` or `fail`. Simple booleans 
with extra information.


Lets's get to it. How do we test in `go`?

- Testing is done on package by package basis
- easiest way to arrange: 
     - Name your test files after the respective files where the functions/objects the'll test reide
     - Go mandates that you append a `_test.go` to the file name. Reason is to make `go test` identify the test files, and `go build` ignore them in builds
     - As I stated earlier, testin is done an a package by package basis. So, every test file in a package will have its package defined as: `(packageName)_test.go`. IOW, all tests in 
        a package have the same package declaration.
     - Each test function starts with `Testxxxx`. Its preferrable to use the name of the function/method you wish to test as the `xxx` part. 
     - The definition of a test function is as such:

      ``` go
        func TextXxx(t *testing.T) {
          Your asserts here
        }
      ```
