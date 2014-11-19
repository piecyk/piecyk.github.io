---
layout: post
title:  "Testing AngularJs"
date:   2014-11-18 23:57:00
categories: angularjs test
---
In era of everyone is saying at every talk and meetup etc... 'Agile and TDD' as a religion,
do people write code in TDD? I know it's hard... but we know the reward in later stages of project.

For me best place to start writing TDD is in AngularJS service/factory abstraction.
I'm always writing (or trying to write) small self canting functions that on input consume data,
transform and return data. In JavaScript world we have tools to write elegant and small code that
easily can be tested ( produce code coverage for our CI environment ).

My stack for testing JavaScript 'not only AngularJS':
- grunt, glup
- karma
- jasmine, mocha && chai && sinon
- phantomjs

let see some code...

{% highlight javascript %}
angular.module("my", []);
angular.module("my").factory("MyService", [
    "Restangular",
    function (Restangular) {
        "use strict";

        var self = {};

        self.getMagic = function(id) {
            return Restangular.one("my-rest/magic", id).get().then(apiResponseTransformer);
        };

        function apiResponseTransformer(data) {
            // magic transform data
            // TODO: here you paly with data
            return [ data, data ];
        }

        // only for unit testing
        self.____unit = function() {
            return angular.extend(self, {
                apiResponseTransformer: apiResponseTransformer,
            });
        };

        return self;
    }
]);

describe("my:factory:MyService", function () {
    "use strict";
    jasmine.getJSONFixtures().fixturesPath = "base/test/fixtures/api";
    var MS, data;

    beforeEach(function (){
        data = getJSONFixture("GET-magic-200.json");
        module("my");
        inject(function(MyService) {
            MS = MyService.____unit();
        });
    });

    it("should check apiResponseTransformer", function (){
        var _data = MCS.apiResponseTransformer(data);
        expect(_data.length).toEqual(2);
    });

);
{% endhighlight %}
