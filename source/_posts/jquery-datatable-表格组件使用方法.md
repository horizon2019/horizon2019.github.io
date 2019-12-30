---
title: jquery datatable 表格组件使用方法
date: 2019-12-24 22:26:12
tags: datatables
category: jquery
---

`1.php后台自定义逻辑部分：`

```

public function productIndexData(\Illuminate\Http\Request $request)
    {
        $data = [];

       //中间需要以数组的形式封装好需要传递的list

        $count = $this->getTotalItemCount($request);

        $data["recordsTotal"] = $count;
        $data["recordsFiltered"] = $count;
        $data["data"] = $productlist;

        return response()->json($data);
    }
```

`Attention:`工具类（后台必须返回 datatables 要求的数据格式，否则不能正常展示数据，该工具类必须返回的数据有 recordsTotal 和 recordsFiltered 和 data 这 3 个参数！）

`2.html部分：`

```
<table class="table table-striped- table-bordered table-hover table-bg table-sort table-checkable"
id="kt_table_1">
<thead style="white-space:nowrap;">
    <tr>
        <th></th>
        <th></th>
        <th></th>
        <th></th>
        <th></th>
        <th></th>
        <th></th>
        <th></th>
    </tr>
</thead>
</table>
```

`3.js部分：`

```
      $(document).ready(function() {
          $("#kt_table_1").DataTable({
              retrieve: true,
              paging: true,
              ordering: true,
              info: true,
              autoWidth: false,
              searching: true,  /* 搜索框 */
              order: [[0, 'desc']],
              bPaginate: true,
              processing: true,
              bAutoWidth: true,
              pagingType: "simple", //分页样式：simple,simple_numbers,full,full_numbers，
              bFilter: false, //去掉搜索方法
              bLengthChange: true, //也是页面上确认是否可以进行选择一页展示多少条
              serverSide: true, //启用服务器端分页，要进行后端分页必须的环节
              ajax: function(data, callback, settings) {

                  // 封装相应的请求参数
                  var pagesize = data.length; //页面显示记录条数，在页面显示每页显示多少项的时候,页大小
                  var start = data.start; //开始的记录序号
                  var page = (data.start) / data.length + 1; //当前页码
                  var order = data.order;
                  var search = data.search;

                  //后端排序
                  if (order[0].column == 0) {
                      ordercolumn = 'id';
                  }
                  if (order[0].column == 9) {
                      ordercolumn = 'validator.created_time';
                  }
                  if (order[0].column == 11) {
                      ordercolumn = 'validator.valid';
                  }


                  var data = {
                      page: page,
                      ordercolumn: ordercolumn,
                      orderdir: order[0].dir,
                      pagesize: pagesize, //这里只传了当前页和页大小，如果有其他参数，可继续封装
                  }

                  //这里需要获取被选择的日期，此处是页面有个日期选择器daterangetimepicker
                  var dateRange = $('#kt_daterangepicker_6 .form-control').val();

                  if (dateRange) {
                      dateRange = dateRange.split("/");
                      var startDate = dateRange[0].replace(/\s/g, '');
                      var endDate = dateRange[1].replace(/\s/g, '');

                  } else {
                      var endDate = moment().format('YYYY-MM-DD HH:mm:ss');
                      var startDate = moment().subtract(14, 'days').format('YYYY-MM-DD HH:mm:ss');

                  }


                  var json = {
                      dataArray: JSON.stringify(data),
                      start_date: startDate,
                      end_date: endDate,
                      search: search['value'].trim(),
                      barcode: $("#samebarcode").val(),
                  }


                  $.ajax({
                      type: "POST",
                      url: "productIndexData",
                      headers: {
                          'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                      },
                      cache: false, //禁用缓存
                      data: json, //传入已封装的参数
                      dataType: "json", //返回数据格式为json
                      success: function(data) {
                          var arr = "";
                          if ('object' == typeof data) {
                              arr = data;
                          } else {
                              arr = $.parseJSON(data); //将json字符串转化为了一个Object对象
                          }
                          console.log("============数据===========")
                          console.log(arr);
                          var returnData = {};
                          //这里直接自行返回了draw计数器,应该由后台返回,可注释掉
                          // returnData.draw = arr.data.pagination.pageCount;
                          //totalCount指的是总记录数
                          returnData.recordsTotal = arr.recordsTotal;
                          //后台不实现过滤功能,全部的记录数都需输出到前端，记录数为总数
                          returnData.recordsFiltered = arr.recordsFiltered;
                          returnData.data = arr.data; //返回用户数据列表
                          console.log(returnData);
                          callback(returnData);


                          //渲染后的tbody element，如果有需要可以这样获取
                          var table = $('#kt_table_1').DataTable();
                          var element = table.table().body();
                      },
                      error: function(XMLHttpRequest, textStatus, errorThrown) {
                          // $dialog.alert("查询失败");
                          // $wrapper.spinModal(false);
                      }
                  });
              },
              columns: [{
                      "data": "skuid",//第一列
                  },
                  {
                      "data": "barcode",
                      orderable: false,
                      render: function(data, type, row, meta) {
                          var barcode = row.barcode;
                          return "<a href=javascript:void(0) " + 'onclick=' +
                              'sameBarcodeSearch' + '(' + barcode + ')' + ">" + barcode +
                              '</a>';
                      }
                  },
                  {
                      "data": "userid",
                      orderable: false,
                  },
                  {
                      "data": "user_name",
                      orderable: false,
                  },
                  {
                      "data": "cart_url",
                      orderable: false,
                      //如果某列需要展示图片
                      render: function(data, type, row, meta) {
                          var skuid = row.skuid;
                          var cart_url = row.cart_url;
                          return '<img src=' + 'https://' + window.location.host +
                              '/downloadPicture?url=' + cart_url + '  width="50px"/>' +
                              '<button style="position:relative;">' + 'replace' + '<input' +
                              ' type="file"' + 'accept="image/*"' +
                              'style="opacity:0;width:100%;height:100%;position:absolute;top:0;left:0"' +
                              'onclick="' + 'getImageInfo(' + skuid + ')' +
                              '" onchange="replaceImage()"  >' + '</button>';

                      }
                  },
                  {
                      "data": "marker",
                      orderable: false,
                      render: function(data, type, row, meta) {
                          if (row.marker_bundle_id != 0) {
                              return 'YES';
                          } else {
                              return 'NO';
                          }
                      }
                  },
              ],

              /* 自定义的一些列操作 */
              columnDefs: [{
                      targets: 10,
                      orderable: false, //禁止排序
                      render: function(data, type, row, meta) {
                          var skuid = row.skuid;
                          return '<button class="' + 'btn btn-primary disabled' +
                              ' kt-badge--inline kt-badge--pill"  onclick="' + 'ajax_action(' +
                              skuid + ')' + '" data-toggle="modal" data-target="#kt_modal_4" ' +
                              'value=' + skuid + '>' + 'Edit' + '</button>';
                      },
                  },
                  {
                      targets: 11,
                      width: '80px',
                      render: function(data, type, row, meta) {
                          var status = {
                              1: {
                                  'title': 'Disable',
                                  'class': 'btn btn-outline-warning'
                              },
                              0: {
                                  'title': 'Enable',
                                  'class': 'btn btn-warning'
                              },
                          };
                          if (typeof status[data] === 'undefined') {
                              return data;
                          }
                          var skuid = row.skuid;
                          var barcode = row.barcode;
                          if (row.creation_verified == 1) {

                              return '<button class="' + status[data].class + ' "  onclick="' +
                                  'ajax_check_action(' + skuid + ',' + barcode + ',' +
                                  "'disable'" + ')' +
                                  '" data-toggle="modal" data-target="#kt_modal_2" >' + status[
                                      data].title + '</button>';
                          }

```
