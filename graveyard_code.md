if (option.ID == 8) {
            window.open(
              'http://sisgedo.regionancash.gob.pe/sisgedonew/app/main.php',
              '_blank'
            );
          }




    function conti(continue: all)  {
      if (option) msgs.push(option) ;
      axios
        .post('http://localhost:5000/', {
          option: option.next ? option.next :'',
          continue:'',

          ID: data.value.ID,
        })
        .then((r: any) => {
          msgs.push(r.data);
          data.value = r.data.continue
          buttons.value = r.data.options;
          data.value = r.data;
        });

    }
    doc('');
