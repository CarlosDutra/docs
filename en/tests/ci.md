# CI测试
---

Framework provides two external testing apis, one is a black box testing, one is the user's browser integration testing (depend on the chromedriver)

```golang
package main

import (
	"github.com/GoAdminGroup/demo/tables"
	"github.com/GoAdminGroup/go-admin/modules/config"
	"github.com/GoAdminGroup/go-admin/tests"
	"github.com/GoAdminGroup/go-admin/tests/common"
	"github.com/GoAdminGroup/go-admin/tests/frameworks/gin"
	"github.com/GoAdminGroup/go-admin/tests/web"
	"github.com/gavv/httpexpect"
	"net/http"
	"testing"
)

// Black box testing
func TestDemoBlackBox(t *testing.T) {
	tests.BlackBoxTestSuit(t, gin.NewHandler, config.DatabaseList{
		"default": config.Database{
			Host:   "127.0.0.1",
			Port:   "3306",
			User:   "root",
			Pwd:    "root",
			Name:   "go_admin_demo_test", // WARNING: test database name must contains "test"
			Driver: "mysql",
		},
	}, tables.Generators, func(cfg config.DatabaseList) {
		// Data cleaner of the framework
		tests.Cleaner(cfg)
		// Clean your own data:
		// ...
	}, func(e *httpexpect.Expect) {
		// Test cases of the framework
		common.Test(e)
		// Write your own API test, for example:
		// More usages: https://github.com/gavv/httpexpect
		e.POST("/signin").Expect().Status(http.StatusOK)
	})
}

// User acceptance testing
func TestDemoUserAcceptance(t *testing.T) {
	web.UserAcceptanceTestSuit(t, func(t *testing.T, page *web.Page) {
		// Write test case base on chromedriver, for example:
		// More usages: https://github.com/sclevine/agouti
		page.NavigateTo("http://127.0.0.1:9033/admin")
		page.Contain("username")
		page.Click("")
	}, func(quit chan struct{}) {
		// start the server:
		// ....
	}, true) // if local parameter is true, it will not be headless, and window not close when finishing tests.
}


```