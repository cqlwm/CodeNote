# 返回Code



```javascript
/**
 * @author lwm
 * @date 2021-05-06 14:01
 * @description
 */
public enum RCode {
    /**
     * 用户名不存在
     */
    USER_NOT_EXIST(State.INFO, 1_100000, "username does not exist"),

    /**
     * 没有管理员权限
     */
    NO_ADMIN_AUTHORITY(State.INFO, 1_100001, "No administrator rights"),

    /**
     * 没有匹配的权限
     */
    NO_MATCH_AUTHORITY(State.INFO, 1_100002, "No matching authority"),

    /**
     * 用户名或者密码错误
     */
    USER_PASSWORD_ERROR(State.INFO, 1_100003, "wrong user name or password"),

    /**
     * 用户已经注册
     */
    USER_ALREADY_REGISTERED(State.INFO, 1_100004, "User already registered"),

    /**
     * 成功
     */
    REQ_SUCCESS(State.SUCCESS,2_100000,  "request is success"),

    /**
     * 客户端参数传递异常
     */
    PARAM_ERROR(State.CLIENT_ERROR,3_100000,  "client transfer param error"),

    /**
     * 服务端错误
     */
    SERVER_ERROR(State.SERVER_ERROR ,4_100000,  "server busy");

    // 编码
    public final int code;

    // 类型
    public final State state;

    // 短语
    public final String reasonPhrase;

    RCode(State state, int code, String reasonPhrase) {
        this.code = code;
        this.state = state;
        this.reasonPhrase = reasonPhrase;
    }

    enum State {
        INFO(1),
        SUCCESS(2),
        CLIENT_ERROR(3),
        SERVER_ERROR(4);
        private final int value;
        State(int value) {
            this.value = value;
        }
    }

}
```

