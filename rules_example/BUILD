load("//:build_settings.bzl", "flavor")
flavor(
    name = "favorite_flavor",
    build_setting_default = False,
    visibility = ["//visibility:public"],
)

load("//:rules.bzl", "drink_rule")
drink_rule(
    name = "my_drink",
)
