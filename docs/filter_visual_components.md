---
title: Filter Visual Components
sidebar_label: Filter Visual Components
copyright: (C) 2007-2018 GoodData Corporation
id: filter_visual_components
---

This article provides examples of filtering visual components by date and attribute values. You can filter the `Visualization` component, `Kpi`, `Headline`, charts and table components with the `filters` prop. The `filters` prop is an array of attribute and date filters. You can make the filters dynamic with the [`AttributeFilter`](attribute_filter_component.md) or [`AttributeElements`](create_custom_attribute_filter.md) components.

Both global filters and measure filters are always interpreted as an intersection of all individual filters \(`f1 AND f2 AND f3...)`.

## Attribute filter

### Positive attribute filter

**Positive attribute filter** lists only those items whose attribute elements' URIs are included in the `in` property array.

```javascript
// Type: IPositiveAttributeFilter
{
    positiveAttributeFilter: {
        displayForm: {
            identifier: '<attribute-displayForm-identifier>' // Or uri: '<attribute-displayForm-uri>'
        },
        in: ['<attribute-element-uri-1>', '<attribute-element-uri-2>'] // Attribute elements currently support only uri
    }
},
```

### Negative attribute filter

**Negative attribute filter** lists only those items whose attribute elements' URIs are _not_ included in the  `notIn` property array.

```javascript
// Type: IPositiveAttributeFilter
{
    negativeAttributeFilter: {
        displayForm: {
            identifier: '<attribute-displayForm-identifier>' // Or uri: '<attribute-displayForm-uri>'
        },
        notIn: ['<attribute-element-uri-1>', '<attribute-element-uri-2>'] // Attribute elements currently support only uri
    }
},
```

## Date filter

### Absolute date filter

You can set a filter to show data that fall within a defined date range with the *absolute date filter*.

```javascript
{
    absoluteDateFilter: {
        dataSet: {
            identifier: '<attribute-display-form-identifier>'
        },
        from: '<YYYY-MM-DD>',
        to: '<YYYY-MM-DD>'
    };
}
```

### Relative date filter

You can set a filter to show data that fall within a date range defined relatively to the current
<!-- TODO: check the wording for "relative date uni / granularity" -->
date. Granularity defines what relative date unit is used (for example, week).

`from` and `to` properties set the number of granularity units (for example, weeks) before or after the current date that define the filter range.

* `0` for the current day, week, month, quarter, or year \(depending on the chosen granularity\)
* `-1` for the previous period
* `-`_`n`_ for the *n*th previous period

Possible values of `granularity` are:

| Value | Description |
| :--- | :--- |
| `'GDC.time.date'` | Days |
| `'GDC.time.week'` | Weeks starting on Monday |
| `'GDC.time.week_us'` | Weeks starting on Sunday |
| `'GDC.time.month'` | Months
| `'GDC.time.quarter'` | Quarters of a year |
| `'GDC.time.year'` | Years |

### Examples

**Last 7 days \(yesterday and 6 days before\):**

```javascript
// Type: IRelativeDateFilter
{
    relativeDateFilter: {
        dataSet: {
            identifier: '<date-dataset-identifier>' // Or uri: '<date-dataset-uri>'
        },
        granularity: 'GDC.time.date',   // Supported values: 'GDC.time.date' | 'GDC.time.week' | 'GDC.time.month' | 'GDC.time.quarter' | 'GDC.time.year'
        from: -7,   // positive or negative whole numbers
        to: -1  // positive or negative whole numbers
    }
}
```

**Last 12 months including the current month**

```javascript
// Type: IRelativeDateFilter
{
    relativeDateFilter: {
        dataSet: {
            identifier: '<date-dataset-identifier>' // Or uri: '<date-dataset-uri>'
        },
        granularity: 'GDC.time.month',  // Supported values: 'GDC.time.date' | 'GDC.time.week' | 'GDC.time.month' | 'GDC.time.quarter' | 'GDC.time.year'
        from: -11,  // positive or negative whole numbers
        to: 0   // positive or negative whole numbers
    }
}
```

**Last quarter only**

```javascript
// Type: IRelativeDateFilter
{
    relativeDateFilter: {
        dataSet: {
            identifier: '<date-dataset-identifier>' // Or uri: '<date-dataset-uri>'
        },
        granularity: 'GDC.time.quarter',    // Supported values: 'GDC.time.date' | 'GDC.time.week' | 'GDC.time.month' | 'GDC.time.quarter' | 'GDC.time.year'
        from: -1,   // positive or negative whole numbers
        to: -1  // positive or negative whole numbers
    }
}
```

## Measure filters

You can also set a filter on a specific measure. This is helpful when you have duplicate measures with diferent filters. Pass an array of attribute or date filters as the `filter` prop inside the measure definition to set a measure filter.

```jsx
<div style={{ height: 300 }}>
    <ColumnChart
        projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
        measures={[{
            measure: {
                localIdentifier: 'totalSales',
                definition: {
                    measureDefinition: {
                        item: {
                            identifier: 'aa7ulGyKhIE5'
                        },
                        filters: [
                            {
                                positiveAttributeFilter: {
                                    displayForm: {
                                        identifier: 'label.restaurantlocation.locationstate'
                                    },
                                    // Attribute elements currently support only uri
                                    in: ['/gdc/md/xms7ga4tf3g3nzucd8380o2bev8oeknp/obj/2210/elements?id=6340116']
                                }
                            }
                        ],
                    }
                },
                alias: 'California sales'
            }
        }]}
    />
</div>
```

## Visualization component filter example

```javascript
import '@gooddata/react-components/styles/css/main.css';
import { Visualization } from '@gooddata/react-components';

<div style={{ height: 400, width: 600 }}>
    <Visualization
        identifier="aaZWa46oh9cJ"
        projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
        filters={[
            {
                positiveAttributeFilter: {
                    displayForm: {
                        identifier: 'label.restaurantlocation.locationstate'
                    },
                    // Attribute elements currently support only uri
                    in: ['/gdc/md/xms7ga4tf3g3nzucd8380o2bev8oeknp/obj/2210/elements?id=6340116']
                }
            }
        ]}
    />
</div>
```

If you reference a saved visualization with active filters and set the `filter` prop on the Visualization component, both sets of filters will be merged using these rules:

* if a saved visualization has an active filter with the same object qualifier (identifier or uri) as the one defined with the `filter` prop,
the saved filter will be overwritten by the one defined in the prop.
* all other filters, both saved and from the prop, will be added.

## Chart component filter example

```jsx
<div style={{ height: 300 }}>
    <ColumnChart
        projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
        measures={[{
            measure: {
                localIdentifier: 'totalSales',
                definition: {
                    measureDefinition: {
                        item: {
                            identifier: 'aa7ulGyKhIE5'
                        }
                    }
                }
            }
        }]}
        filters={[
            {
                negativeAttributeFilter: {
                    displayForm: {
                        identifier: 'label.restaurantlocation.locationstate'
                    },
                    // Attribute elements currently support only uri
                    notIn: ['/gdc/md/xms7ga4tf3g3nzucd8380o2bev8oeknp/obj/2210/elements?id=6340116']
                }
            }
        ]}
    />
</div>
```

## AttributeFilter example

`AttributeFilter` renders a dropdown list of all values of the selected attribute. `AttributeFilter` has the `onApply` function property. This function is called when the user clicks the Apply button in the filter dropdown. The function receives an attribute filter with the selected (positive filter) or not selected (negative filter) attribute values.

```jsx
import React, { Component } from 'react';
import { AttributeFilter, ColumnChart } from '@gooddata/react-components';

import '@gooddata/react-components/styles/css/main.css';

const employeeNameIdentifier = 'label.employee.employeename';
const totalSalesIdentifier = 'aa7ulGyKhIE5';

export class AttributeFilterExample extends Component {
    constructor(props) {
        super(props);
        this.state = {
            filters: []
        };
    }

    onApply = (filter) => {
        console.log('AttributeFilterExample filter', filter);

        const isPositive = !!filter.in;
        const elementsProp = isPositive ? 'in' : 'notIn';

        const filters = [{
            [isPositive ? 'positiveAttributeFilter' : 'negativeAttributeFilter']: {
                displayForm: {
                    uri: filter.id
                },
                [elementsProp]: filter[elementsProp].map(element => (`/gdc/md/xms7ga4tf3g3nzucd8380o2bev8oeknp/obj/2200/elements?id=${element}`))
            }
        }];

        this.setState({ filters });
    }

    render() {
        const { filters } = this.state;
        return (
            <div>
                <AttributeFilter
                    identifier={employeeNameIdentifier}
                    projectId={projectId}
                    fullscreenOnMobile={false}
                    onApply={this.onApply}
                />
                <div style={{ height: 300 }}>
                    <ColumnChart
                        projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
                        measures={[{
                            measure: {
                                localIdentifier: 'totalSales',
                                definition: {
                                    measureDefinition: {
                                        item: {
                                            identifier: totalSalesIdentifier
                                        }
                                    }
                                }
                            }
                        }]}
                        filters={filters}
                    />
                </div>
            </div>
        );
    }
}
```

[See live example](https://gooddata-examples.herokuapp.com/attribute-filter-components)

## AttributeElements example

Pass a custom children function to `AttributeElements`. This function will receive a parameter with a list of attribute values of the selected attribute. You can use this to render any custom attribute filter.

```jsx
<AttributeElements
    identifier="label.employee.employeename"
    projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
    options={{ limit: 20 }}
>
    {({ validElements, loadMore, isLoading, error }) => {
        const {
            offset = null,
            count = null,
            total = null
        } = validElements ? validElements.paging : {};
        if (error) {
            return <div>{error}</div>;
        }
        const filters = validElements
            ? [{
                positiveAttributeFilter: {
                    displayForm: {
                        identifier: 'label.employee.employeename'
                    },
                    in: validElements.items.map(item => (
                       item.element.uri
                    ))
                }
            }]
            : [];
        return (
            <div>
                <div>
                    {validElements ? validElements.items.map(item => (
                       item.element.title
                    )).join(', ') : null}
                </div>
                <button
                    onClick={loadMore}
                    disabled={isLoading || (offset + count === total)}
                >More
                </button>
                <ColumnChart
                    projectId="xms7ga4tf3g3nzucd8380o2bev8oeknp"
                    measures={[{
                        measure: {
                            localIdentifier: 'totalSales',
                            definition: {
                                measureDefinition: {
                                    item: {
                                        identifier: 'aa7ulGyKhIE5'
                                    }
                                }
                            }
                        }
                    }]}
                    filters={filters}
                />
            </div>
        );
    }}
</AttributeElements>
```

[See live example](https://gooddata-examples.herokuapp.com/attribute-filter-components)