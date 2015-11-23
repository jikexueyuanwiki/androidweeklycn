# 用Robolectric进行参数化测试 

在我们目前的项目中，我们使用 [Robolectric](https://github.com/robolectric/robolectric) 为 Android 应用程序编写单元测试，并且它一直都很好用。最近我需要编写一个操作需要执行几次并且需要用到不同的测试数据的一个测试用例，还要断言正确的行为发生取决于数据。 

对于所谓的[参数化测试](https://github.com/junit-team/junit/wiki/Parameterized-tests)，Junit 具有一个易于使用的选项，其中一项里面定义了测试数据，然后可以使用[参数化测试运行器](https://github.com/junit-team/junit/blob/master/src/main/java/org/junit/runners/Parameterized.java)来执行测试。这将为测试数据的每一个元素创建测试类的一个实例，其中测试数据被传递给构造函数。 

事实证明，Robolectric 有一个完全一样的[参数化 Robolectric 测试运行器](https://github.com/robolectric/robolectric/blob/master/robolectric/src/main/java/org/robolectric/ParameterizedRobolectricTestRunner.java)（但略微有些调整，以符合Robolectric），并且在验证应用程序接收来自外部服务提供商的不同的错误代码的行为测试时工作的非常好。 

```
    @RunWith(ParameterizedRobolectricTestRunner.class)
	public class ContactServiceTest {
 
    	@ParameterizedRobolectricTestRunner.Parameters(name = "ErrorCode = {0}")
    	public static Collection<Object[]> data() {
        	return Arrays.asList(new Object[][]{
                	{105, 105_ERROR_MSG},
                	{113, 113_ERROR_MSG},
                	{114, 114_ERROR_MSG},
                	{134, 134_ERROR_MSG},
                	{137, 137_ERROR_MSG},
                	{999, DEFAULT_ERROR_MSG} // Bogus error code
        	});
    	}
 
    	private int errorCode;
    	private String expectedErrorMsg;
 
    	public ContactServiceTest(int errorCode, String errorMsg) {
        	this.errorCode = errorCode;
        	this.expectedErrorMsg = errorMsg;
    	}
 
    	@Test
    	public void when_known_error_code_is_received_from_service_correct_error_msg_is_displayed_to_user() {
        	// HTTP response from service contains defined error code
        	Robolectric.addPendingHttpResponse(HttpStatus.SC_OK, buildFakeServiceResponse(errorCode)); 
        	// Contact the service
        	mService.contactService();
        	// Use awaitility to wait until error message is displayed to user
        	// then assert that the error message is correct
        	await().until(getDisplayedErrorMsg(), is(expectedErrorMsg));
        } 
```

代码将执行测试用例六次，依次为测试数据的每个元素进行测试，并将显示的错误消息看成是特定错误代码定义的错误信息。每个测试运行将被视为自己的测试用例并作为报告进行创建。在参数注释中添加名称参数，这样可以重新整理测试结果。正如显示的测试运行的结果，测试用例的名字在这种情况下将是 

```
when_known_error_code_is_received_from_service_correct_error_msg_is_displayed_to_user[ErrorCode = 105]
    when_known_error_code_is_received_from_service_correct_error_msg_is_displayed_to_user[ErrorCode = 113]
    when_known_error_code_is_received_from_service_correct_error_msg_is_displayed_to_user[ErrorCode = 114]
    ... 
```

对于进一步检验 [Junit 理论启示](https://github.com/junit-team/junit/wiki/Theories)或者说是 [Junit快速检查](https://github.com/pholser/junit-quickcheck)，一种很好的方法是在你的 Junit（也就是 Robolectric）测试中生成测试数据，而不是你自己去定义它。

